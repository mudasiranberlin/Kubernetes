
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

