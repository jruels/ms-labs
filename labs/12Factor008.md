# 12Factor Lab 8

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the eighth of 12 Factor App principles, **[Concurrency](https://12factor.net/concurrency)**.

## What is Concurrency?

According to the website, [12 Factor App](https://12factor.net/concurrency), 

* *In the twelve-factor app, processes are a first class citizen. Processes in the twelve-factor app take strong cues from the unix process model for running service daemons. Using this model, the developer can architect their app to handle diverse workloads by assigning each type of work to a process type. For example, HTTP requests may be handled by a web process, and long-running background tasks handled by a worker process.*

## What you'll be doing 

In this scenario you''ll be working with a version of the *Food Court* application that has been refactored into a distributed application that runs in a Kubernetes Cluster. We're running *Food Court* under the [Kubernetes](https://kubernetes.io/docs/home/) container orchestration framework because it makes it possible to organize the code components of *Food Court's* into Kubernetes [services](https://kubernetes.io/docs/concepts/services-networking/service/), [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) and [pod](https://kubernetes.io/docs/concepts/workloads/pods/) resources. The resources are easy to manage declaratively under Kubernetes using resource files that define the particular configuration of the given resource.

In this lab we're going to demonstrate the 12 Factor App principle of Concurrency by scaling different portions of *Food Court* up according to the process type.

The steps you'll take in this scenario are as follows:

* **Part 1:** Installing the Source Code from GitHub
* **Part 2:** Getting the Code Up and Running in a Kubernetes
* **Part 3:** Learning About Process Concurrency According to Type
* **Part 4:** Affecting the Demonstration Application's Process Concurrency

## About the demonstration application
The demonstration application used in the scenario is a version of the *Food Court* project we've worked with previously. However, in this lab , *Food Court* has been refactored to run under a Kubernetes Cluster.

![Food Court](12factor-008/assets/foodcourt.jpg)

Each component is represented as a [Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/). Each service is backed by a [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) of pods. You're going to affect concurrency by adjusting the `replicas` attribute in the manifest `yaml` file for some of the deployments.

**Caution** Since this lab will be running in a different kubernetes environment than it was configured for, some of the pod restarts will fail with Docker container errors, but you should be able to still see the point of the lab.

---
## Part 1: Cloning the Lesson Code from GitHub
The objective of this lesson is to demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab08`

`cd lab08`


**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 2:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

**Step 3:** Start kubernetes

Minikube is an environment that runs a single node kubernetes cluster in a VM (we are using VirtualBox) for test and dev purposes

`minikube start`

This will take awhile but you are looking for a line that looks like this:

```
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

**Step 5:** Confirm Kubernetes is up and running and the nodes are accessible

`kubectl get nodes`

You'll get output similar to the following:

```
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   54s   v1.17.0

```

---

## Part 2: Getting the Code Up and Running in a Kubernetes

## Objective
The objective of this lesson is demonstrate how to get the demonstration code up and running

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab08/12factor && pwd`{{execute T1}}

You should see the following output:

`/home/ubuntu/lab08/12factor`

**Step 2:** Check out the `git` branch that contains the source code for this lesson:

`git checkout 8-concurrency.0.0.1`

You'll see output as follows:

```
Branch '8-concurrency.0.0.1'
set up to track remote branch '8-concurrency.0.0.1' from 'origin'.
Switched to a new branch '8-concurrency.0.0.1'

```

Create the container images the scenario needs and store them in the Local Container Repository running on `localhost:5000`.

**Step 3:** Execute the bash script that executes the provisioning

`sh docker-seed.sh`

**Step 4:** Confirm that the expected the docker images in the Local Container Registry

`curl http://localhost:5000/v2/_catalog`

You should see the following as output:

```
{"repositories":["burgerqueen","collector","customer","hobos","iowafried"]}

```

**Step 5**  Seed Kubernetes

`cd k8s && sh generate-k8s-resources.sh`

**Step 6** Confirm all the deployments and services are running

`kubectl get deployment`

You'll get the following output:

```
deployment.apps/burgerqueen-deployment created
deployment.apps/collector-deployment created
deployment.apps/customer-deployment created
deployment.apps/hobos-deployment created
deployment.apps/iowafried-deployment created
deployment.apps/redis-deployment created
service/burgerqueen created
service/collector created
service/customer created
service/hobos created
service/iowafried created
service/redis created

```

You'll see output similar to the following:

```
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
burgerqueen-deployment   1/1     1            1           2m3s
collector-deployment     1/1     1            1           2m2s
customer-deployment      1/1     1            1           2m2s
hobos-deployment         1/1     1            1           2m1s
iowafried-deployment     1/1     1            1           2m1s
redis-deployment         1/1     1            1           2m

```

However, there may be a 0/1 for the READY column because of a configuration problem. The error can be seen in the creation of the Docker containers

`kubectl get pods`  

Check the service

`kubectl get service`{{execute T1}}

You get output similar to the following:

```
NAME          TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)        AGE
burgerqueen   ClusterIP      10.99.38.60      <none>         80/TCP         5s
collector     ClusterIP      10.98.97.42      <none>         80/TCP         5s
customer      LoadBalancer   10.98.104.91     10.98.104.91   80:32122/TCP   4s
hobos         ClusterIP      10.102.225.246   <none>         80/TCP         4s
iowafried     ClusterIP      10.105.87.146    <none>         80/TCP         4s
kubernetes    ClusterIP      10.96.0.1        <none>         443/TCP        7m14s
redis         ClusterIP      10.98.138.176    <none>         80/TCP         3s

```

Take notice of the `EXTERNAL-IP` of the service `customer`. This is the entry point service into *Food Court* within the Kubernetes cluster. In this case the `EXTERNAL-IP` is `10.98.104.91`.

**Step 6** If the service is running at an external IP, confirm that Food Court is running in the Kubernetes cluster by making executing `curl` against `customer`. (Notice that `customer` is running on port `80`. Thus, when we call `curl` we can execute using the service name only and expect that the default HTTP, `80`, port of will be used.

`curl 10.98.104.91`

You get output similar to the following (The restaurant will differ.):

```

{"restaurant":"Iowa Fried Chicken","order":"20 Piece Bucket"},"customer":"Friendly Shopper"}

```

---


## Part 3: Learning About Process Concurrency According to Type

## Objective
The objective of this lesson is to explain the concept of process types and how you can use process types to support the [Concurrency](https://12factor.net/concurrency) principle of 12 Factor App.

## Understanding concurrency

A process type is an grouping of application processes according to semantic definition. For example, in the demonstration application *Food Court* there are three process types, `consumer`, `provider` and `persistence`. (The names we assign to the process type group are arbitrary.)

![Food Court](12factor-008/assets/process-types.jpg)

The `customer` service is a process type, `consumer` because it consumes data from the `provider` type.

The `provider` process type are the restaurants in *Food Court*, `burgerqueen`, `iowafried` and `hobos`. These restaurants provide data in the form of meals to the `customer`.

The `persistence` provider types because the persist data from the `provider` process types.

The relevance to the the [Concurrency](https://12factor.net/concurrency) principle of 12 Factor App is this: you want to have your application segmented according to process type so that you can scale of a particular process type independent of other process types. For example, if too much stress is applied the `collector`, you can simply increase the number of `collector` process independent of the other process types.

The same is true of the `provider` process types. If `burgerqueen` becomes too stressed, we can scale up its processes. (In this case all processes are Kubernetes pods. Each pod contains an instance of the given restaurant.)

The benefit of [Concurrency](https://12factor.net/concurrency) is that there it offers a great deal flexibility in terms of scale and resource consumption.

The following steps will show you the current state of the process (pod) allocation of the components that make up the demonstration application, *Food Court*.

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab08/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab08/12factor`

**Step 2:** Take a look at the pods running the Kubernetes cluster

`kubectl get deployment`

You'll see out put similar to the following:

```
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
burgerqueen-deployment   1/1     1            1           2m3s
collector-deployment     1/1     1            1           2m2s
customer-deployment      1/1     1            1           2m2s
hobos-deployment         1/1     1            1           2m1s
iowafried-deployment     1/1     1            1           2m1s
redis-deployment         1/1     1            1           2m

```

Each of [Kubernetes deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) shown above represent a collection of pods (processes) running under a corresponding Kubernetes service. Notice that each deployment has only 1 pod running. (The `READY` column. `1/1` means that each deployment has been allocated 1 pod and one is running.)

Now, what were to happens if the `customer`, `hobos` and `collector` components were subject to increased stress, but the levels of stress were different for each? Of course we would increase the number of processes running for each component. But, because we're support the [Concurrency](https://12factor.net/concurrency) principle of 12 Factor App, we can increase the pod allocation for each separately and independently.

We'll increase the allocations accordingly in the next lesson. 


---

## Part 4: Affecting the Demonstration Application's Process Concurrency

## Objective
The objective of this lesson is demonstrate how to increase pod allocation in the Kubernetes cluster in which the demonstration application *Food Court* is running.

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab08/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab08/12factor`

**Step 2:** Take a look at the current allocation of pods in the various deployments.

`kubectl get deployment`

You'll see out put similar to the following:

```
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
burgerqueen-deployment   1/1     1            1           2m3s
collector-deployment     1/1     1            1           2m2s
customer-deployment      1/1     1            1           2m2s
hobos-deployment         1/1     1            1           2m1s
iowafried-deployment     1/1     1            1           2m1s
redis-deployment         1/1     1            1           2m

```

As we discusses in the last lesson, we imagined a situation in which the processes that are represented by pods in the Kubernetes cluster under which became subject increases stress, but the level of stress varied among components. In this case, because we are supporting the Concurrency principle of 12 Factor App, we can increase the number of pods for each affected service independently.

We'll do this by taking advantage of the a feature in Kubernetes deployments that supports increasing number number of pods under s deployment by doing nothing more than increasing the value assigned to `replicas` attribute in the given deployment manifest `yaml` file.

In this case, we're going to increase the number of pods associated with the `collector` service from `1` to `2`.

**Step 3:** View the actual manifest file that will instigate the pod allocation for `collector` by executing the following command:

`clear && cat k8s/manifests/collector-deployment-update.yaml`

We'll increase the number of pods associated with the `customer` service from `1` to `3`.

**Step 4:** View the manifest file that will instigate the pod allocation for `customer` by executing the following command:

`clear && cat k8s/manifests/customer-deployment-update.yaml`

We'll increase the number of pods associated with the `hobos` service from `1` to `4`.

**Step 5:** View the manifest file that will instigate the pod allocation for `hobos ` by executing the following command:

`clear && cat k8s/manifests/hobos-deployment-update.yaml`

Now, let's do the update using the bash script that's part of this lesson.

**Step 6:** Change the the number of pods assigned to each deployment to accommodate the stress points we described above

`cd k8s && sh update-k8s-deployments.sh`

You get out put similar to the following:

```
deployment.apps/collector-deployment configured
deployment.apps/customer-deployment configured
deployment.apps/hobos-deployment configured

```

**Step 7:** Take a look at the news allocation of pods in the various deployments.

`kubectl get deployment`

You get out put simlar to the following:

```
burgerqueen-deployment   1/1     1            1           3m42s
collector-deployment     2/2     2            2           3m42s
customer-deployment      3/3     3            3           3m41s
hobos-deployment         4/4     4            4           3m41s
iowafried-deployment     1/1     1            1           3m41s
redis-deployment         1/1     1            1           3m40s

```

As you can see the component allocations have been updated as expected.

**Step 7:** Shut down kubernetes

`minikube stop`

---

**Congratulations! You've completed the lab.**

