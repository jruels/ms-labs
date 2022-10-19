# 12Factor Lab 4

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the fourth of 12 Factor App principles, **[Backing Services](https://12factor.net/backing-services)**.

## What is a Backing Service?

According to the website, [12 Factor App](https://12factor.net/config), a backing service is any service that the web service or application consumes over the network as part of its normal operation.

*"Backing services like the database are traditionally managed by the same systems administrators who deploy the app’s runtime. In addition to these locally-managed services, the app may also have services provided and managed by third parties. Examples include SMTP services (such as [Postmark](https://postmarkapp.com/), metrics-gathering services (such as [New Relic](https://newrelic.com/) or [Loggly](https://www.loggly.com/)), binary asset services (such as [Amazon S3](https://aws.amazon.com/s3/)), and even API-accessible consumer services (such as [Twitter](https://twitter.com/), [Google Maps](https://www.google.com/maps), or [Last.fm](https://www.last.fm/)).*

*The code for a twelve-factor app makes no distinction between local and third party services. To the app, both are attached resources, accessed via a URL or other locator/credentials stored in the config. A deploy of the twelve-factor app should be able to swap out a local MySQL database with one managed by a third party (such as Amazon RDS) without any changes to the app’s code.*


## What you'll be doing 

In this lab you'll doing following:

* **Part 1:** Cloning the Lesson Code from GitHub
* **Part 2:** Understanding the Benefit of Using a Backing Service
* **Part 3:** Binding an Application to a Backing Service
* **Part 4:** Creating a Backing Service
* **Part 5:** Using a Backing Service

## Executing command line instructions 


All of the labs use Linux bash commands. If you are not familiar with the commands, ask your instructor for clarification

## Using the VM

You will be logged into a Ubuntu VM machine. You should create a new directory in your home directory for each lab to avoid having the source code conflict with code from previous labs.

## About the demonstration application

This lab uses a demonstration application named, *Pinger*. The purpose of *Pinger* is to report environmental information of a webserver at runtime. We'll be adding features to *Pinger* throughout the labs in order to demonstrate the principles of 12 Factor App. In order to control development, a new branch will be created in this project's GitHub repository each time a feature set is created.

Also, new in this lesson we'll add an addition demonstration application named, *Collector*. *Collector* is a simple RESTful API that represents a backing service. *Collector* takes in data in JSON format via an HTTP POST. *Collector* will then store the data in a [Redis](https://redislabs.com/) database. The reasoning behind *Collector* will be discussed throughout the lesson.

---
## Part 1: Getting the code and Starting REDIS

The objective of this lesson is demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab04`

`cd lab04`

**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

**Step 3:** This lesson requires a running instance of Redis. So, we'll install it as a Docker container.

`docker run --name innoredis -p 6379:6379 -d redis`

You'll see output similar, but not exactly like the following:

`b58182cd32e6ebaeeacca2492ea308fffb1242adff32b2d606d5d7ba778dbc17`

We'll do a fast check of Redis by going into the Redis container and executing a `ping` command against Redis which will prove the application is working.

**Step 4:** Go into the Redis container's command line

`docker exec -it innoredis sh`{{execute}}

You'll see the container's command prompt as output:

`#`

**Step 5:** Start the `redis-cli` tool so that you can communicate with the `redis` database

`redis-cli`

You'll the following output which is the command line for the `redis-cli`:

`127.0.0.1:6379>`

**Step 6:** Execute the `ping` command at the command line:

`ping`

You'll get the following output:

`PONG`

**Step 7:** Exit out the the `redis-cli`:

`exit`

You'll see the following output

`#`

**Step 8:** Exit `redis` container

`exit`

You'll see the following output

`$`

You have exited Redis and are back at the command prompt of the virtual machine in the interactive learning environment.

---

## Part 2: Understanding the Benefit of Using a Backing Service

The objective of this part is to develop an understanding of the benefit of using the [Backing Services](https://12factor.net/backing-services) principle of 12 Factor App in order to make an application flexible, scalable and easier to change.

In this lab we're going to evolve the lab's demonstration application to learn how to delegate data storage to an external resource. Using data storage as a backing service is typical in the 12 Factor App.

## Key Concept: 12 Factor App - Back Services
A key concept in the **Backing Services** principle of 12 Factor App is that critical, but fundamentally independent logic should be part of the application's source code. For example, imagine that you are logging incoming HTTP requests to a file in the hosting machine's file system. At some point the risk that the log files will eat up capacity is real. Or, if the machine fails for some reason. Then the log data is lost.

Now granted there are a lot of precautionary measures you can take to prevent such hazard on the file system, but by using the principle of Backing Server can have log data to to an external resource. The external resource might be another file server on the network or even a database. But, the important things is that the identified resource will be designed in such as way as to absorb a near infinite amount of log data. This way your application is doing what it's designed to do and log management, which is not really the intention of your service, is delegated elsewhere.

Also, an intrinsic concept in Backing Service is transportability. In other words, you should program your service so that you can easily disconnect from one backing service and bind to an alternate on demand one. (We'll cover this concept in depth throughout the scenario.)

## Steps

**Step 1:** Navigate back to the the lesson's working directory.

`cd /home/ubuntu/lab04/12factor && pwd`

You should see the following output:

`/home/ubuntu/lab04/12factor`

**Step 2:** Check out the an earlier version of *Pinger* from the local `git` repo that we've just cloned from GitHub

`git checkout 4-backing-services.0.0.1`

You'll see output as follows:

```
Branch '4-backing-services.0.0.1' set up to track remote branch '4-backing-services.0.0.1' from 'origin'.
Switched to a new branch '4-backing-services.0.0.1'

```
**Step 3:** Take a look at the contents of the demonstration application's working directory.

`clear && tree -L 2`

You'll see output as follows:

```
.
├── collector
│   ├── datastore
│   ├── index.js
│   ├── package.json
│   └── test
└── pinger
    ├── logger
    ├── package.json
    ├── readme.md
    ├── server.js
    └── test

```

Notice that this branch of the source introduces an application named `collector` in addition to the demonstration application `pinger` that we've been working with in previous lessons.

`collector` will be the backing service to `pinger`. `collector` will collect and store log data from `pinger`. `collector` is designed to store large amounts of log data so that `pinger` can do what it needs to do without having to work about storage capacity for logging.

**Step 4:** Take a look at the webserver code for `pinger` by loading the file in the the `vi` editor:

`vi ./pinger/server.js`

**Step 5:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

Notice that a `logger` is defined at Line 5.

Go to Line 37:

`:37`

You'll see code like this:

`logger.info(request)`

Here's what's going on. The code is logging the incoming `request` so that there's an audit trail about what's going on. A good question to ask is ths: *Where is the `logger` sending the data?* In order to answer this question we need to look at the code for `logger`, which we'll do in the next lesson when we look at binding to a backing service.

But, before moving forward answering this question we need to get out of `vi`.

**Step 6:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 7:** Exit `vi`

`:q!`

You have exited `vi`.

---

## Part 3: Binding an Application to a Backing Service

The objective of this lesson is to bind the demonstration application to the Backing Service created in the last lesson.

In the previous lesson you examined the source code for *Pinger's* web server. You'll have noticed the the code as using a `logger` to keep a record of requests coming into *Pinger*.

In this lesson we'll look at the details of the the logger as well as the environment configuration settings that binds *Pinger* to the backing service *Collector*. *Collector* is the backing service to which the logger binds.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab04/12factor`

If the working directory is not `/home/ubuntu/lab04/12factor`, execute the following command:

`cd /home/ubuntu/lab04/12factor`

**Step 2:** Open the the logger's source code in `vi`.

`vi ./pinger/logger/index.js`

**Step 3:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

Notice the code at `Line 4`:

```
const url = `http://${process.env.COLLECTOR_HOSTNAME}:${process.env.COLLECTOR_PORT}`

```

The variable `url` defines the location on the network where the logger will send data. The actual logging mechanism that sends the log data to the HTTP endpoint defined by the variable `url` is in the external Node.js packages, [pino](https://www.npmjs.com/package/pino) and [pino-http-send](https://www.npmjs.com/package/pino-http-send) that are declared at `Lines 1 - 2` at the start of the logger source code.

At the logical level what's happening is that the program sends the incoming `request` to the logger and the logger sends the data to be logged on to the endpoint on the Internet. The declaration of the endpoint URL is made int ghe logger source code. But, again when we examine the critical piece of code:

```
const url = `http://${process.env.COLLECTOR_HOSTNAME}:${process.env.COLLECTOR_PORT}`

```
You'll see that the URL definition is composed of values from the environment variables `COLLECTOR_HOSTNAME}` and `COLLECTOR_PORT`. The question is, where and how are these environment variables set. To answer this question we need to look at the `.env` file for *Pinger*, which we'll do in a moment. But first let's get out of `vi`.

**Step 4:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 5:** Exit `vi`

`:q!`

You have exited `vi`.

**Step 6:** Now, let's take a look at *Pinger's* `.env` file.

`clear && cat ./pinger/.env`

You'll get the following output:

```
PINGER_PORT=3040
PINGER_ADMIN=true
CURRENT_VERSION="3-config.0.0.1"
COLLECTOR_HOSTNAME=localhost
COLLECTOR_PORT=4001

```

The `.env` file contains the values for the environment variables `COLLECTOR_HOSTNAME` && `COLLECTOR_PORT`. Thus, in this case the endpoint for the backing service is, `http://localhost:4001`.

Putting the values that define the endpoint to the backing service into environment variables should seem familiar because this is an prime example of the third principle of 12 Factor App - Config.

In the next part we'll look the details of creating backing service.

---

## Part 4: Creating a Backing Service

The objective of this lesson is to see a demonstration of how teh backing service, *Collector* was created.

All the source code for *Collector* in directory, `./12factor/collector`. Well examine the files in this directory during our analysis.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab04/12factor`

If the working directory is not `/home/ubuntu/lab04/12factor`, execute the following command:

`cd /home/ubuntu/lab04/12factor`

**Step 2:** View the contents of the directory, `./12factor/collector`

`clear && tree ./collector -L 2`

You'll see output as follows:

```
./collector
├── datastore
│   └── index.js
├── index.js
├── package.json
└── test
    └── api-tests.js

2 directories, 4 files
```

The backing service *Collector* has two parts. The first is the file,`index.js` which is the source code for the API which receives the HTTP request for the logger. The second is the code that binds the *Collector* to the underlying Redis database. This code is in the file `./datastore/index.js`.

Let's look at the API code first.

**Step 3:** Open the source code for the *Collector* API in the `vi` editor:

`vi ./collector/index.js`

**Step 3:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

`Lines 16 -28` contain the code that accepts the HTTP POST request.

Notice that `Line 23` is the code that does the work of saving the incoming request to the Redis database. This is the code:

`const rslt = await write(newData);`

The `write` method which is used at `Line 23` is declared earlier in the file at `Line 5`. The `write` method is the result of a Node.js `exports` from the `./collector/datastore/index.js` file. This file is where the Redis binding takes place. We'll look at this next, but first we need to close down the `vi` editor. 

**Step 4:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 5:** Exit `vi`

`:q!`

---

**A NOTE ABOUT USING REDIS:**

In this demonstration we're representing the backing service that collects log data as a custom API that binds to an associated Redis database. While it's true that could have bound the `logger` in *Pinger* directly to the Redis database and not really violated the 12 Factor principle of Back Service, binding the `logger` directly to Redis impairs the ease of rebinding to a storage mechanism other than Redis.

The `logger` in *Pinger* emits JSON which is a widely supported data format. And the underlying network protocol is simple HTTP. Thus, `logger` can bind to any backing service that accepts JSON and HTTP. However, were we to have the logger bind to Redis directly, then the only other backing service for data storage we could use is Redis. To use another backing service such as Kafka would involve going into the `logger` source code and rewriting a lot of it to support Kakfa.

Representing Redis through and RESTful API that accepts JSON via a POST makes things simple and versatile.

---

**Step 6:** Open the file, `./collector/datastore/index.js` in the `vi` editor

`vi ./collector/datastore/index.js`

**Step 7:** Turn on the line numbering the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

*Collector* binds to Redis using the Node.JS [Redis package](https://www.npmjs.com/package/redis) on NPM. The code that imports the package is at `Line 1`. All the work of interacting with Redis is done by the package. All the *Collector* needs to provide is the connection URL which is done at `Line 6` at the statement:

`const client = redis.createClient({host,port});`

Notice that the code does look for the environment variables, `COLLECTOR_DELEGATE_HOSTNAME` and `COLLECTOR_DELEGATE_PORT` at `Lines 1 -2`, following the 12 Factor App principle of Config. But, if the environmental variables are not provided, the code will default to `localhost:6379` which is the default host and port for Redis. This is done as a matter of convenience for the developer.

The actual work of writing the the Redis database is done at `Lines 18 - 26`.

The code will read from the Redis database according the key value created at `Lines 19` using the `uuidv4()` method from the Node.js [uuid](https://www.npmjs.com/package/uuid) package and passed by the call to Redis at `Line 20`, via the `setAsync` method which is derived from the Node.JS [Redis package](https://www.npmjs.com/package/redis).

As you can see, the backing service, *Collector* is a [RESTful](https://restfulapi.net/) API that represents a Redis database.

Now that we've analyzed data write features of *Collector* close up the `vi` editor.

**Step 8:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 9:** Exit `vi`

`:q!`

You have exited `vi`.


At this point you should have a conceptual sense of how the application *Pinger* use the backing service *Collector* to store log data.

In the next part we'll make spin up both *Pinger* and *Collector* and demonstrate the code in action.

---

## Part 5: Using a Backing Service

The objective of this lesson is to provide an example of the *Pinger* webservice using the backing service *Collector*.

## What you'll be doing

In this lesson we're going to start the both the *Pinger* and the associate backing service *Collector*. (This are both Node.js applications running as a webserver). The *Collector* backing service will bind automatically to the underlying Redis database that we started in a Docker container at the beginning of this scenario.

Once every thing is up and running, we'll make a call to to *Pinger* using `curl`. Calling `curl` will generate a request to *Pinger*. *Pinger* will record the request using its `logger` and then create and return a response. The `logger` in turn will send the contents of the request onto the backing service, *Collector*. (We covered how `logger` works in a previous lesson.

We'll do a call to *Pinger* using `curl`. Then, we'll check the keys of the data stored in the Redis database from the command line. Inspecting the Redis keys will correlate with the request logging activity that took in *Pinger*.

## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab04/12factor`

If the working directory is not `/home/ubuntu/lab04/12factor`, execute the following command:

`cd /home/ubuntu/lab04/12factor`

**Step 2:** Install the dependencies for both the *Pinger* and *Collector* applications.

`cd /home/ubuntu/lab04/12factor/pinger && npm install && cd /home/ubuntu/lab04/12factor/collector && npm install && cd /home/ubuntu/lab04/12factor`

**Step 3:** In a second terminal window, start up *Collector*.

`cd /home/ubuntu/lab04/12factor/collector && node index.js`

You will see output similar to the following (the date will be different):

```
Collector Server started on port 4001 at Thu Dec 10 2020 20:28:55 GMT+0000 (Coordinated Universal Time)

```

**Step 4:** In a third terminal window, start up *Pinger*.

`cd /home/ubuntu/lab04/12factor/pinger && node server.js`

You will see output similar to the following (the date will be different):

```
Pinger is listening on port 3040 at Thu Dec 10 2020 20:54:34 GMT+0000 (Coordinated Universal Time)

```

**Step 5:** In the first terminal window, make a `curl` call to *Pinger* which is running on `localhost:3040`.

`clear && curl localhost:3040`

**Step 6:** Navigate into the shell of the Docker container the is running Redis

`docker exec -it innoredis sh`

You'll see the container's command prompt as output:

`#`

**Step 7:** Start the `redis-cli` tool so that you can communicate with the `redis` database

`redis-cli`

You'll the following output which is the command line for the `redis-cli`:

`127.0.0.1:6379>` 

**Step 8:** Get the keys to all the records stored in the Redis database by executing the Redis `KEYS` command under the Redis CLI.

 `KEYS *`
 
 As a return you will get a list of Universally Unique Identifiers (UUID), like so:
 
 ```
1) "803e5081-9d63-42d8-9075-16e0f109ef08"
 
 ```
 
 In this case because we've only make one request to *Pinger* the single log entry that cascaded from *Pinger* onto *Collector* and into the the Redis database is reflected in the single UUID shown in the list returned from Reds.
 
Now, return to the command prompt of the virtual machine in the interactive learning environment.
 
 **Step 11:** Exit out the the `redis-cli`:

`exit`

You'll see the following output

`#`

**Step 12:** Exit `redis` container

`exit`

You'll see the following output

`$`

You have exited Redis and are back at the command prompt of the virtual machine in the interactive learning environment.

 **Step 12:** Shut down the servers

 Go into the two windows that you started the servers in and shut them both down with a control-c

 Shut down the docker redis container

 `docker stop innoredis`

 Remove the stopped container.

 `docker container prune -f`


---

**Congratulations! You've completed the lab**






