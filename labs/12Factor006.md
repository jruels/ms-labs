# 12Factor Lab 6

## Objective

TThe objective of this lab is to demonstrate the basic concepts behind of the sixth of 12 Factor App principles, **[Processes](https://12factor.net/processes)**.

## What is Processes?

The Processes principle that states that a service or application must run in distinct, stateless process. State is held an ancillary process according the 12 Factor principle of [Backing service]([Processes](https://12factor.net/backing-service)).

According to the website, [12 Factor App](https://12factor.net/build-release-run), 

* *Twelve-factor processes are stateless and share-nothing. Any data that needs to persist must be stored in a stateful backing service, typically a database.*
* *Some web systems rely on “sticky sessions” – that is, caching user session data in memory of the app’s process and expecting future requests from the same visitor to be routed to the same process. Sticky sessions are a violation of twelve-factor and should never be used or relied upon. Session state data is a good candidate for a datastore that offers time-expiration, such as Memcached or Redis.*

## What you'll be doing 

In this scenario you'll be installing, analyzing and running a demonstration application named, *Food Court*. *Food Court* is a collection of Docker containers. Each container runs as a stateless process in compliance with the 12 Factor principle of Processes.


![Food Court](12factor-006/assets/foodcourt.jpg)

The steps you'll take in this scenario are as follows:

* **Part 1:** Cloning the Lesson Code from GitHub
* **Part 2:** Provisioning the Runtime Environment
* **Part 3:** Understanding the Nature of Stateless Processes
* **Part 4:** Running the Demonstration Application

## About the demonstration application

The demonstration application *Food Court* emulates a food court in a shopping mall. The food court is surrounded by three food counters, `Burger Queen`, `Hobos` and `Iowa Fried`.

Each restaurant has its own distinct menu. Thus, when a customer in the food court buys an item from a restaurant, the customer is getting an item that is special the menu of the given restaurant.


---
## Part 1: Cloning the Lesson Code from GitHub
The objective of this lesson is to demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab06`

`cd lab06`


**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 2:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

---

## Part 2: Provisioning the Runtime Environment

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab6/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab6/12factor `

**Step 2:** Check out the `git` branch that contains the source code for this lesson:

`git checkout 6-processes.0.0.1`

You'll see output as follows:

```
Branch '6-processes.0.0.1' set up to track remote branch '6-processes.0.0.1' from 'origin'.
Switched to a new branch '6-processes.0.0.1'

```

Install the stateless processes as a collection of Docker containers running under [Docker Compose](https://docs.docker.com/compose/).

**Step 3:** Execute the bash script that executes the provisioning

`sh docker-seed.sh`


**Step 4:** Confirm that the expected the docker images in the Local Container Registry

`curl http://localhost:5000/v2/_catalog`

You should see the following as output:

`{"repositories":["burgerqueen","customer","hobos","iowafried"]}`

**Step 5:** In a second terminal window, start the application using Docker Compose under the Docker network named, `westfield_mall`.

`cd ~/lab06/12factor && docker-compose up`


---

## Part 3: Understanding the Nature of Stateless Processes

The objective of this lesson is examine the structure of the demonstration application, *Food Court* to see how the code support notion of a stateless processes as prescribed by the 12 Factor App principle of Processes

## Steps

**Step 1:** View the file and directory structure of the demonstration application

`tree ./ -L 1`

You'll get the following output:

```
./
├── burgerqueen
├── customer
├── docker-compose.yaml
├── docker-seed.sh
├── hobos
├── iowafried
└── readme.md

```
The directories, `burgerqueen`, `customer`, `hobos` and `iowafried` in the tree above contain the code for each of the processes that will be represented in a distinct container.

The file, `docker-seed.sh` is a utility script the creates a Local Docker Registry and then creates a docker image according to the `Dockerfile` that is stored along with the application code in each process directory. The docker image that is created is then stored in the Local Docker Registry.

**Step 2:** View the contents of `docker-seed.sh`

`cat docker-seed.sh`

Each process runs as a webserver making it so the only means of interaction between processes is via standard [HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) between the web servers.

None of the processes/webservers maintain state. In the case of `customer`, all it knows how to do is to find a `restaurant` at random. All a `restaurant` knows is how to pick an order off its menu at random and deliver it to the caller. The restaurant has no idea about the customer other than the information it's given at the time of the request.

Separating the code into isolated processes makes maintaining the code for each process easier.

**Step 3:** List the docker images in the Local Container Registry

`curl http://localhost:5000/v2/_catalog`

You'll get the following output:

```
{"repositories":["burgerqueen","customer","hobos","iowafried"]}

```

**WHERE**

* **customer** is a process that is an emulation of a customer buying food at a food counter in a food court
* **burgerqueen** is a food counter in the food court
* **hobos** is a food counter in the food court
* **iowafried** is a food counter in the food court

Each of these images is a representation of the distinct, stateless containers that will make up the processes for the entirety of the demonstration application.

## Part 4: Running the Demonstration Application

### Objective
The objective of this lesson is exercise the *Food Court* demonstration application that we've installed and invoked in previous lessons.

In previous lessons we installed the demonstration application, *Food Court* as a set of processes represented as containers that run under Docker Compose.

Running the processes as independent containers complies with the 12 Factor principle of Processes.

The only entry point into the *Food Court* application is to call the `customer` process which will, in turn, call one of the restaurant processes at random. (Docker Compose was configured to only expose the process `customer` at port 4000.)

Calling the `customer` process via `localhost:4000` will make it so that the `customer` subsequently call a restaurant process.

## Steps

**Step 1:** Make 20 calls on the application using `curl`;

`for i in {1..20}; do curl localhost:4000 -w "\n"; done`

You'll get output similar to, but not exactly like the following:

```
{"restaurant":"Howard Bonsons","order":"grilled cheese","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"fries","customer":"Friendly Shopper"}
{"restaurant":"Iowa Fried Chicken","order":"Chix Pack","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"grilled cheese","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"burger","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"fried shrimp","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"fried shrimp","customer":"Friendly Shopper"}
{"restaurant":"Iowa Fried Chicken","order":"Spicy Wings","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"soda and fries","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"whooper","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"double burger","customer":"Friendly Shopper"}
{"restaurant":"Iowa Fried Chicken","order":"Spicy Wings","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"grilled cheese","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"whooper","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"grilled cheese","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"fried shrimp","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"fries","customer":"Friendly Shopper"}
{"restaurant":"Burger Queen","order":"whooper","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"grilled cheese","customer":"Friendly Shopper"}
{"restaurant":"Howard Bonsons","order":"fried shrimp","customer":"Friendly Shopper"}
```

Notice that there have been 20 calls to the`customer` process and that in turn each call to `customer` created a call to a restaurant. The output above is responses from `customer`. `customer` reports which `restaurant` it called, what the `order` was, as well as the name of the `customer `making the purchase.

**Step 2:** Shut down the application

In the window the server is running, enter a control-C to terminate the application. 

## Summary

The important thing to understand about the *Food Court* application is that it demonstrates the 12 Factor App principle of Processes. Each of the components that make up the *Food Court* application exists as distinct processes in their own process space. Each process is independent. No code is shared among any.

---
**Congratulations! You've completed the lab.**