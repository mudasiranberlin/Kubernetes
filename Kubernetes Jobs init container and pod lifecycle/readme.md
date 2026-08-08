# Kubernetes Jobs, CronJobs & Pod Lifecycle

## 📌 Overview

This tutorial demonstrates how Kubernetes can run **one-time tasks** and **scheduled tasks** using:

* Kubernetes **Jobs**
* Job **parallelism**
* `activeDeadlineSeconds`
* Kubernetes **CronJobs**
* Pod lifecycle
* `restartPolicy`
* Job completion and termination

A **Job** is useful when we want a task to run and eventually finish.

A **CronJob** is useful when we want Kubernetes to automatically create Jobs on a schedule.

---

# 1. Kubernetes Job

## What is a Job?

A Kubernetes **Job** creates one or more Pods and makes sure that a specified task runs successfully.

For example:

```text
Job
 |
 └── Pod
      |
      └── Container
           |
           ├── Run command
           ├── Finish task
           └── Exit
```

Once the task finishes successfully, the Job is marked as:

```text
Complete
```

---

# Create a Job

Create the YAML file:

```bash
vi job.yaml
```

Use:

```yaml
apiVersion: batch/v1
kind: Job

metadata:
  name: testjob

spec:
  template:
    metadata:
      name: testjob

    spec:
      containers:
        - name: counter
          image: centos:7
          command: ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]

      restartPolicy: Never
```

---

# Job YAML Explanation

## `apiVersion`

```yaml
apiVersion: batch/v1
```

The `batch/v1` API is used for Kubernetes batch resources such as Jobs and CronJobs.

---

## `kind`

```yaml
kind: Job
```

Tells Kubernetes that we are creating a Job.

Jobs are designed for tasks that:

* Start
* Perform work
* Finish
* Report completion

---

## Job Name

```yaml
metadata:
  name: testjob
```

The Job is named:

```text
testjob
```

---

# Pod Template

```yaml
template:
```

The Job itself does not directly contain the container.

Instead, it contains a **Pod template**.

The Job uses this template to create Pods.

```text
Job
 |
 └── Pod Template
       |
       └── Pod
            |
            └── Container
```

---

# Container

```yaml
containers:
  - name: counter
    image: centos:7
```

The container is named:

```text
counter
```

and uses:

```text
centos:7
```

---

# Container Command

```yaml
command:
  ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]
```

The container executes:

```bash
echo Technical-Guftgu
```

Then:

```bash
sleep 5
```

So the container:

1. Prints `Technical-Guftgu`
2. Waits for 5 seconds
3. Finishes
4. Exits successfully

The Job then becomes:

```text
Complete
```

---

# `restartPolicy: Never`

```yaml
restartPolicy: Never
```

The Pod will not restart the container after it exits.

This is commonly used with Jobs.

The Job controller is responsible for creating another Pod if another attempt is required.

---

# Create the Job

Run:

```bash
kubectl apply -f job.yaml
```

---

# Check Jobs

```bash
kubectl get jobs
```

Example:

```text
NAME      STATUS     COMPLETIONS   DURATION
testjob   Complete   1/1           5s
```

The important part is:

```text
1/1
```

This means one successful completion was achieved.

---

# Check Pods

```bash
kubectl get pods
```

You may see:

```text
testjob-xxxxx   0/1   Completed   0   10s
```

The Pod has finished its task.

---

# Watch Pod Lifecycle

You can continuously watch Pods:

```bash
watch kubectl get pods
```

You will see the Pod move through its lifecycle.

For example:

```text
ContainerCreating
        ↓
Running
        ↓
Completed
```

Because the command sleeps for 5 seconds, the Pod will run for approximately 5 seconds before completing.

---

# View Job Details

```bash
kubectl describe job testjob
```

---

# View Pod Logs

First find the Pod:

```bash
kubectl get pods
```

Then:

```bash
kubectl logs <pod-name>
```

Expected output:

```text
Technical-Guftgu
```

---

# Delete the Job

To delete the Job:

```bash
kubectl delete job testjob
```

You can also delete using the YAML file:

```bash
kubectl delete -f job.yaml
```

---

# 2. Job with Parallelism and Active Deadline

Now we will create a Job that:

* Runs multiple Pods at the same time.
* Has a maximum execution time.
* Terminates Pods when the deadline is reached.

Create:

```bash
vi job2.yaml
```

YAML:

```yaml
apiVersion: batch/v1
kind: Job

metadata:
  name: testjob

spec:
  parallelism: 5
  activeDeadlineSeconds: 10

  template:
    metadata:
      name: testjob

    spec:
      containers:
        - name: counter
          image: centos:7
          command: ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 20"]

      restartPolicy: Never
```

---

# `parallelism`

```yaml
parallelism: 5
```

This tells Kubernetes that up to **5 Pods can run at the same time** for this Job.

Conceptually:

```text
             Job
              |
      ---------------------
      |    |    |    |    |
     Pod  Pod  Pod  Pod  Pod
      |    |    |    |    |
      ---------------------
```

All Pods execute the same task.

---

# `activeDeadlineSeconds`

```yaml
activeDeadlineSeconds: 10
```

This sets the maximum amount of time the Job is allowed to remain active.

In this example:

```text
Job deadline = 10 seconds
```

But the container command says:

```bash
sleep 20
```

Therefore:

```text
Container wants to run: 20 seconds
Job deadline:            10 seconds
```

The Job cannot continue running beyond its active deadline.

Kubernetes terminates the active Pods when the deadline is reached.

---

# Why Does the Job Fail?

The container runs:

```bash
echo Technical-Guftgu
sleep 20
```

But:

```yaml
activeDeadlineSeconds: 10
```

means the Job is only allowed to remain active for 10 seconds.

Therefore:

```text
0 sec
 |
 | Container starts
 |
 | echo Technical-Guftgu
 |
 | sleep 20
 |
10 sec
 |
 | Job deadline reached
 |
 | Pods terminated
 |
20 sec
```

The Pods do not get enough time to finish the 20-second sleep.

---

# Create the Job

```bash
kubectl apply -f job2.yaml
```

---

# Watch the Pods

Run:

```bash
watch kubectl get pods
```

You may see Pods such as:

```text
testjob-4f4qx   1/1   Terminating   0   19s
testjob-6ntz6   1/1   Terminating   0   19s
testjob-bsdrm   1/1   Terminating   0   19s
```

The exact Pod names will be different in your cluster.

The important part is:

```text
Terminating
```

This indicates Kubernetes is terminating the Pods because the Job's active deadline has been reached.

---

# Delete Job 2

```bash
kubectl delete -f job2.yaml
```

Or:

```bash
kubectl delete job testjob
```

---

# 3. CronJob

## What is a CronJob?

A Kubernetes **CronJob** creates Jobs according to a schedule.

It is similar to Linux `cron`.

For example, you can configure a task to run:

* Every minute
* Every hour
* Every day
* Every week
* At a specific time

The structure is:

```text
CronJob
   |
   ├── Job
   |    |
   |    └── Pod
   |         |
   |         └── Container
   |
   ├── Job
   |    |
   |    └── Pod
   |
   └── Job
        |
        └── Pod
```

---

# Create a CronJob

Create:

```bash
vi cronjob.yaml
```

Use:

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: bhupi

spec:
  schedule: "* * * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: bhupi
              image: ubuntu
              command: ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]

          restartPolicy: Never
```

---

# Cron Schedule

The following schedule:

```yaml
schedule: "* * * * *"
```

means:

```text
Every minute
```

The five fields are:

```text
*     *     *     *     *
│     │     │     │     │
│     │     │     │     └── Day of week
│     │     │     └───────── Month
│     │     └─────────────── Day of month
│     └───────────────────── Hour
└─────────────────────────── Minute
```

---

# Cron Format

The format is:

```text
Minute Hour Day-of-Month Month Day-of-Week
```

Examples:

| Schedule    | Meaning                  |
| ----------- | ------------------------ |
| `* * * * *` | Every minute             |
| `0 * * * *` | Every hour               |
| `0 0 * * *` | Every day at midnight    |
| `0 9 * * *` | Every day at 9:00 AM     |
| `0 0 * * 0` | Every Sunday at midnight |

---

# CronJob Container

The container uses:

```yaml
image: ubuntu
```

and executes:

```bash
echo Technical-Guftgu
```

then:

```bash
sleep 5
```

So each Job runs for approximately 5 seconds.

---

# Create the CronJob

Run:

```bash
kubectl apply -f cronjob.yaml
```

---

# Check CronJobs

```bash
kubectl get cronjobs
```

Example:

```text
NAME     SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE
bhupi    * * * * *      False     0        ...
```

---

# Check Jobs Created by CronJob

Run:

```bash
kubectl get jobs
```

Every minute, Kubernetes creates a new Job.

For example:

```text
bhupi-12345678
bhupi-12345679
bhupi-12345680
```

Each Job creates a Pod.

---

# Watch CronJob Pods

Run:

```bash
watch kubectl get pods
```

You will see the lifecycle repeatedly:

```text
Pod created
    ↓
Running
    ↓
Container executes command
    ↓
sleep 5
    ↓
Completed
    ↓
Next minute
    ↓
New Job
    ↓
New Pod
```

---

# CronJob Lifecycle

The complete process is:

```text
                 CronJob
                    |
             Every 1 minute
                    |
                    ↓
                  Job
                    |
                    ↓
                  Pod
                    |
                    ↓
               Container
                    |
                    ↓
        echo Technical-Guftgu
                    |
                    ↓
                 sleep 5
                    |
                    ↓
               Completed
                    |
                    |
              Wait 1 minute
                    |
                    ↓
              New Job created
```

---

# Important Difference: Job vs CronJob

| Feature            | Job                  | CronJob                         |
| ------------------ | -------------------- | ------------------------------- |
| Purpose            | Run a task           | Schedule a task                 |
| Runs automatically | Once                 | According to schedule           |
| Creates Pods       | Yes                  | Creates Jobs, which create Pods |
| Example            | Database backup once | Database backup every day       |
| API                | `batch/v1`           | `batch/v1`                      |

---

# Job vs Deployment

A Job is different from a Deployment.

### Deployment

Used for long-running applications:

```text
Web Server
API Server
Nginx
Backend Application
```

Example:

```text
Pod → Running continuously
```

### Job

Used for tasks that should finish:

```text
Backup
Database migration
Data processing
Batch processing
```

Example:

```text
Pod
 ↓
Run task
 ↓
Finish
```

---

# Pod Lifecycle in a Job

A typical Job Pod can move through states such as:

```text
Pending
   ↓
Running
   ↓
Succeeded
```

If something goes wrong:

```text
Pending
   ↓
Running
   ↓
Failed
```

With `restartPolicy: Never`, the container itself is not restarted inside the same Pod after termination. The Job controller may create another Pod when another attempt is needed.

---

# Useful Commands

## Create Resource

```bash
kubectl apply -f job.yaml
```

---

## Create CronJob

```bash
kubectl apply -f cronjob.yaml
```

---

## List Jobs

```bash
kubectl get jobs
```

---

## List CronJobs

```bash
kubectl get cronjobs
```

---

## List Pods

```bash
kubectl get pods
```

---

## Watch Pods

```bash
watch kubectl get pods
```

---

## Describe Job

```bash
kubectl describe job testjob
```

---

## Describe CronJob

```bash
kubectl describe cronjob bhupi
```

---

## View Job Logs

```bash
kubectl logs <pod-name>
```

---

## Delete Job

```bash
kubectl delete job testjob
```

---

## Delete CronJob

```bash
kubectl delete cronjob bhupi
```

---

## Delete Using YAML

```bash
kubectl delete -f job.yaml
```

```bash
kubectl delete -f cronjob.yaml
```

---

# 🧠 Quick Revision

Remember it like this:

```text
JOB
 ↓
Run task
 ↓
Finish
```

```text
CRONJOB
 ↓
Wait for schedule
 ↓
Create Job
 ↓
Create Pod
 ↓
Run task
 ↓
Finish
 ↓
Wait for next schedule
 ↓
Create another Job
```

### Job

**"Run this task."**

### CronJob

**"Run this task according to this schedule."**

### `parallelism`

**"Run multiple Pods at the same time."**

### `activeDeadlineSeconds`

**"Stop the Job after this amount of active time."**

### `restartPolicy: Never`

**"Do not restart the container inside the Pod after it exits."**

---

# ✅ Summary

In this tutorial, we learned:

* ✅ How to create a Kubernetes Job
* ✅ How a Job creates a Pod
* ✅ How a container completes a Job
* ✅ How `restartPolicy: Never` works with Jobs
* ✅ How to use `parallelism`
* ✅ How to use `activeDeadlineSeconds`
* ✅ How Kubernetes terminates Pods after the Job deadline
* ✅ How to create a CronJob
* ✅ How Cron expressions work
* ✅ How a CronJob creates Jobs
* ✅ How Jobs create Pods
* ✅ How to monitor Pod and Job lifecycle
* ✅ How to delete Jobs and CronJobs

Kubernetes **Jobs** are useful for tasks that need to finish, while **CronJobs** are useful when those tasks need to run repeatedly on a schedule.













# Kubernetes Jobs,init container and pod lifecycle


vi job.yaml

=========================

apiVersion: batch/v1

kind: Job

metadata:
  name: testjob

spec:
  template:
    metadata:
      name: testjob

    spec:
      containers:
        - name: counter

          image: centos:7

          command: ["bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]

      restartPolicy: Never


# Explanation:
# This Job creates a Pod that runs a task and completes automatically.
#
# apiVersion: batch/v1
# Uses the Kubernetes batch API for creating Jobs.
#
# kind: Job
# Defines that we are creating a Job resource.
# Jobs are used for running one-time tasks that should finish successfully.
#
# metadata.name: testjob
# Name of the Job is testjob.
#
# template
# Defines the Pod configuration that the Job will create.
#
# container name: counter
# The container inside the Pod is named counter.
#
# image: centos:7
# Uses CentOS 7 Docker image to run the container.
#
# command:
# ["bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]
#
# Runs the following commands inside the container:
# 1. Prints:
#    Technical-Guftgu
#
# 2. Waits for 5 seconds.
#
# After the command finishes, the Job is marked as completed.
#
# restartPolicy: Never
# The Pod will not restart after completion or failure.
# Kubernetes creates a new Pod only if the Job needs another attempt.

=======================

kubectl apply -f  job.yaml


kubectl get jobs

kubectl apply -f  job.yaml

kubectl delete jobs testjob

watch kubectl get pods

You can see in the watch after 5 second the job will complete and status show complete



====================================================

now another Jon container will delete in 30 seconds and after that 10 seconds job done 
means 40 seconds job finished 


vi pod2.yaml

apiVersion: batch/v1

kind: Job

metadata:
  name: testjob

spec:
  parallelism: 5
  activeDeadlineSeconds: 10

  template:
    metadata:
      name: testjob

    spec:
      containers:
        - name: counter

          image: centos:7

          command: ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 20"]

      restartPolicy: Never


# Explanation:
# This Job creates Pods that perform a one-time task.
#
# parallelism: 5
# Kubernetes runs 5 Pods at the same time.
# All Pods execute the same command in parallel.
#
# activeDeadlineSeconds: 10
# The Job is allowed to run for a maximum of 10 seconds.
# If the Job is still running after 10 seconds, Kubernetes stops the Job and terminates the Pods.
#
# template
# Defines the Pod that the Job creates.
#
# container name: counter
# The container is named "counter".
#
# image: centos:7
# Uses the CentOS 7 Docker image.
#
# command:
# /bin/bash -c "echo Technical-Guftgu; sleep 20"
#
# The container:
# 1. Prints "Technical-Guftgu".
# 2. Sleeps for 20 seconds.
#
# Since the Job timeout is only 10 seconds,
# the Pods will be terminated before completing the 20-second sleep.
#
# restartPolicy: Never
# Failed or completed Pods are not restarted automatically.



kubectl apply -f  job2.yaml


you will see using this command 

watch kubectl get pods

testjob-4f4qx   1/1     Terminating   0          19s
testjob-6ntz6   1/1     Terminating   0          19s
testjob-bsdrm   1/1     Terminating   0          19s


kubectl delete -f job2.yaml

============================================



cron job utility   run in 5minutes or other time then we can use it 


apiVersion: batch/v1
kind: CronJob

metadata:
  name: bhupi

spec:
  # Run every minute
  schedule: "* * * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: bhupi
              image: ubuntu
              command: ["/bin/bash", "-c", "echo Technical-Guftgu; sleep 5"]

          restartPolicy: Never



*  *  *  *  *
│  │  │  │  │
│  │  │  │  └── Day of week
│  │  │  └───── Month
│  │  └──────── Day of month
│  └─────────── Hour
└────────────── Minute


kubectl apply -f cronjob.yml

kubectl get jobs

it will create the create the container and also in 5 seconds will delete and after 1 minute it will create another and like that




