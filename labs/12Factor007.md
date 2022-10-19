# 12Factor Lab 7

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the seventh of 12 Factor App principles, **[Port Binding](https://12factor.net/port-binding)**.

## What is Port Binding?

The Port Binding principle is that a web application or service should expose itself to the network as a port number. Any web server or other protocol that supports network traffic should be internal to the web application or service.

According to the website, [12 Factor App](https://12factor.net/port-binding), 

* *The twelve-factor app is completely self-contained and does not rely on runtime injection of a webserver into the execution environment to create a web-facing service. The web app exports HTTP as a service by binding to a port, and listening to requests coming in on that port.*
* *HTTP is not the only service that can be exported by port binding. Nearly any kind of server software can be run via a process binding to a port and awaiting incoming requests. Examples include ejabberd (speaking XMPP), and Redis (speaking the Redis protocol)*

## What you'll be doing 

In this scenario we're going to revisit the demonstration application *Food Court* that we examined in the last scenario about the principle of [Processes](https://12factor.net/processes). Only this time we're going to modify *Food Court* by changing the ports on which the constituent restaurant services run, thus demonstrating the benefit of the Port Binding principle. The way we are going ao manipulate port binding is by way of the third principle of 12 Factor App, [Configuration](https://12factor.net/config).

The code in this version of the *Food Court* application has been refactored to make it responsive to changes in the port bindings. All of this well be revealed as you do the lessons in the lab.

The steps you'll take in this scenario are as follows:

* **Part 1:** Installing the Source Code from GitHub
* **Part 2:** Getting the Code Up and Running
* **Part 3:** Learn the Value of Port Binding
* **Part 4:** Binding to Services by Port Number


---
## Part 1: Cloning the Lesson Code from GitHub
The objective of this lesson is to demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab07`

`cd lab07`


**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 2:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

---

## Part 2: Provisioning the Runtime Environment

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab7/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab7/12factor `

**Step 2:** Check out the `git` branch that contains the source code for this lesson:

`git checkout 7-port-binding.0.0.1`

You'll see output as follows:

```
Branch '7-port-binding.0.0.1'
set up to track remote branch '7-port-binding.0.0.1' from 'origin'.
Switched to a new branch '7-port-binding.0.0.1'
```

Install the stateless processes as a collection of Docker containers running under [Docker Compose](https://docs.docker.com/compose/).

**Step 3:** Execute the bash script that executes the provisioning

`sh docker-seed.sh`


**Step 4:** Confirm that the expected the docker images in the Local Container Registry

`curl http://localhost:5000/v2/_catalog`

You should see the following as output:

`{"repositories":["burgerqueen","customer","hobos","iowafried"]}`

**Step 5:** In a second terminal window, start the application using Docker Compose under the Docker network named, `westfield_mall`.

`cd ~/lab07/12factor && docker-compose up`

Notice the as the containers in *Food Court* come online under Docker Compose that the web service `customer` is running on port `3000` like so:

```
customer_1     | Friendly Shopper API Server is listening on port 3000

```

While the restaurant web services are running on port `3001`.

```
hobos_1        | Howard Bonsons API Server is started on 3001  at Sun Dec 13 2020 23:47:47 GMT+0000 (UTC) with pid6
iowafried_1    | Iowa Fried Chicken API Server is started on 3001  at Sun Dec 13 2020 23:47:46 GMT+0000 (UTC) withpid 6
burgerqueen_1  | Burger Queen API Server is started on 3001  at Sun Dec 13 2020 23:47:48 GMT+0000 (UTC) with pid 6

```
This is something to remember as we'll talk about port numbers more in upcoming lessons.

**Step 6:** Make 20 calls on the application using `curl`;

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
As you see the application is up and running properly.


---

## Part 3: Learn the Value of Port Binding

## Objective
The objective of this lesson is analyze the source code and configuration files to discover how these element comply with the 12 Factor App principle of [Port Binding](https://12factor.net/port-binding).

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab07/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab07/12factor`

**Step 2:** Take a look at the `customer` source code in `vi`.

`vi ./customer/index.js`

**Step 3:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

The `customer` web service has two purposes. The first purpose is to allow access to it by allowing those outside the Docker network named, `westfieldmall` to make an HTTP request on the application hostname and port that's exposed publicly. The second purpose of `customer` is to contact a constituent restaurant at random to get an order. These restaurants are accessible internally on the Docker network `westfieldmall`. The hostname of each restaurant is the service name under which the restaurant runs as defined in the `docker-compose.yaml` file.

Let's do a short analysis of code.

Notice that `Line 2` defines the port on which the `customer` web server will be listening. The port value is assigned either from the environment variable, `APP_PORT`. If `APP_PORT` does not exist, the default value, `3000` is assigned.

Line 3 is the port where `customer` will make an HTTP request to contact any constituent restaurant. The port of the restaurants is defined by the environmental variable, `RESTAURANT_PORT`.

The assumption is that all restaurants will be running on the same port. This is not a problem because each restaurant will be running under its own DNS host name the gets assigned automatically by Docker Compose. As mentioned above, the host name corresponds to the service under which the restaurant is defined in `docker-compose.yaml`. (We going to look at a port assignment and service name definition in `docker-compose.yaml` in the next lesson.

**The important thing** to understand is that each service in *Food Court* is completely self-sustaining. No web server is injected nor is any DNS host name known directly. DNS names are assigned at run time by the Docker Compose domain name server. All any of the web services really know is its port number. This the essential concept behind the principle of Port Binding. **Apps and services are exposed via a port number.**

Let's get out of `vi` and take a look at some code for a restaurant.

![portbinding](12factor-007/assets/portbinding.jpg)

**Step 4:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 5:** Exit `vi`

`:q!`

You have exited `vi`.

Let's take a look at the code for the constituent restaurant service `burgerqueen`/

**Step 6:** Take a look at the source code for the restaurant `burgerqueen` in `vi`.

`vi ./burgerqueen/index.js`

**Step 7:** Turn on the line numbering the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

Notice `Line 2` in the `burgerqueen` code:

```
const port = process.env.APP_PORT || 3000;

```
 `Line 2` is where the restaurant service, in this case `burgerqueen` get the port on which it will run. As with the other service it looks to get its value from the environment variable, `APP_PORT`. If the environment variable does not exists, it uses the default port, `3000`. That the `customer` and the restaurant services, `burgerqueen`, `hobos` and `iowafried` are all using the same environment variable name is permissible because each of these services will run in a container. Thus, each container will have its own instance of the environment variable.
 
The important thing is this: as with `customer` each restaurant service is self-contained and each restaurant service presents itself via its port number.

**Step 8:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step y:** Exit `vi`

`:q!`

You have exited `vi`.

---

## Part 4: Binding to Services by Port Number

## Objective
The objective of this lesson is analyze how ports are assigned ti services in the demonstration application.

As mentioned previously, port configuration is done in the `docker-compose.yaml` file. Let's open the file and take a look at the contents.

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

``cd /home/ubuntu/lab07/12factor && pwd``{{execute T1}}

You should see the following output:

`home/ubuntu/lab07/12factor`

**Step 2:** Open the `docker-compose.yaml` source code in `vi`.

`vi docker-compose.yaml`

**Step 3:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

The port settings in *Food Court* for the `customer` service are declared at `Lines 6 - 7`. The environment variable `APP_PORT` declares the port that the `customer` web server will listen for incoming requests. The environment variable `RESTAURANT_PORT` declares the port to where `customer` will a send HTTP request to restaurant.

The DNS names for the services are defined in the comma delimited string that is assigned to the environment variable, `RESTAURANT_DNS_NAMES` at `Line 8`. Thus, if `customer` wants to send a request to the *Burger Queen* service URL is:

`http://burgerqueen:3001`

The service names in the comma delimited string that's assigned to the environment variable,`RESTAURANT_DNS_NAMES` correspond to the service names defined for the Docker Compose configuration at `Lines 13, 19 and 25.` (The technique for service name definition is part of the way Docker Compose works.)

Also, you can see that each of the restaurants services have an environment variable, `APP_PORT` which is assigned the value `3001`. This means that the web server's in each of the restaurant services will be listening for incoming request on port `3001`.


**Step 4:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 5:** Exit `vi`

`:q!`

You have exited `vi`.

**Step 6:** Shut down the application

In the window the server is running, enter a control-C to terminate the application. 

## Summary

The key concept of the 12 Factor App principle of Port Binding is that no matter what, the port number is the way that the service represents itself to the network and the internals of the service respect the port number. The service does not hard code DNS names.

DNS naming is done by the run time environment. This is why we had to inject the DNS names of the restaurants into the `customer` in the demonstration application, *Food Court*.

A pattern has emerged over the years in which certain well known products and services will publish default port numbers that become conventional. For example [MySQL](https://dev.mysql.com/) default to port to `3306`. [MongoDB's](https://docs.mongodb.com/manual/reference/default-mongodb-port/) default port is `27017`. Web servers run by default on port `80`. And, secure web servers running under `HTTPS` run on port `443`. Also, the SSH servers listen on port `22`.

---

**Congratulations! You've completed the lab**