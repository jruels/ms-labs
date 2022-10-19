# 12Factor Lab 9

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the nineth of 12 Factor App principles, **[Disposability](https://12factor.net/disposability)**.

## What is Disposability?

According to the website, [12 Factor App](https://12factor.net/disposability), 

* *Processes should strive to minimize startup time. Ideally, a process takes a few seconds from the time the launch command is executed until the process is up and ready to receive requests or jobs. Short startup time provides more agility for the release process and scaling up; and it aids robustness, because the process manager can more easily move processes to new physical machines when warranted.*
* *Processes shut down gracefully when they receive a SIGTERM signal from the process manager. For a web process, graceful shutdown is achieved by ceasing to listen on the service port (thereby refusing any new requests), allowing any current requests to finish, and then exiting. Implicit in this model is that HTTP requests are short (no more than a few seconds), or in the case of long polling, the client should seamlessly attempt to reconnect when the connection is lost.*

In other words, Disposability is about making sure that your web app or service starts and and shuts down in a safe efficient manner that is surprise free.

## What you'll be doing 

In this lab we're going to examine the shutdown code in the restaurant service, *Burger Queen* that is part of the *Food Court* web application.

You'll be doing the following steps:

* **Part 1:** Installing the Source Code from GitHub
* **Part 2:** Getting the Code Up and Running
* **Part 3:** Instigating Shutdown of a Service
* **Part 4:** Analyzing the Shutdown Code

---

## Part 1: Installing the Source Code from GitHub

## Steps

**Step 1:** Get the code from GitHub:

Create a working directory from your home directory for the lab and locate to it:

`mkdir lab09`

`cd lab09`

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 2:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor && pwd`

**Step 3:** Check out the `git` branch that contains the source code for this lesson:

`git checkout 9-disposability.0.0.1`

You'll see output as follows:

```
Branch '9-disposability.0.0.1'
set up to track remote branch '9-disposability.0.0.1 from 'origin'.
Switched to a new branch '9-disposability.0.0.1'

```

---

## Part 2: Getting the Code Up and Running

The objective of this lesson is demonstrate how to get the demonstration code up and running

## Steps

**Step 1:** Go to the directory of the *Burger Queen* service of *Food Court*.

`cd burgerqueen`

**Step 2:** Install the application's dependencies

`npm install`

**Step 3:** Run the unit tests

`npm test`

You'll get output similar to the following:

```
> burgerqueen@1.0.0 test /root/12factor/burgerqueen
> mocha test

Burger Queen, API Server is started on 3000  at Sat Dec 19 2020 17:49:30 GMT+0000 (Coordinated Universal Time), with pid 2446
  API Tests:
{ restaurant: 'Burger Queen', order: 'whooper' }
    ✓ Can access GET item /

  API Tests:
{
  status: 'SHUTDOWN',
  shutdownMessage: 'Burger Queen API Server shutting down at Sat Dec 19 2020 17:49:30 GMT+0000 (Coordinated Universal Time)',
  pid: 2446
}

```

**Step 4:** Start up the *Burger Queen* service

`clear && node index.js`

You'll see the output similar to the following:

```
Burger Queen, API Server is started on 3000  at Sat Dec 19 2020 17:28:52 GMT+0000 (Coordinated Universal Time), with pid 3921

```

The *Burger Queen* service is up and running.

---


## Part 3: Instigating Shutdown of a Service

The objective of this lesson is demonstrate how instigate a service shutdown and to observe an example of graceful termination as recommended by the Disposability principle of 12 Factor App.

## Steps

**Step 1:** Send a `ctrl+c` exist command to the process running the *Burger Queen* service in the first terminal window


You'll get the following output:

```
{
  status: 'SHUTDOWN',
  shutdownMessage: 'Signal SIGINT : Burger Queen API Server shutting down at Sat Dec 19 2020 17:35:59 GMT+0000 (Coordinated Universal Time)',
  pid: 3921
}

```

Notice that the *Burger Queen* service as emitted a message in JSON that reports that the service is shutting down along with information about the time that the service was terminated and the process id in which the service was running.

This a basic indicator that the service is intending to do a graceful shut down. Of course, the internals of the service must also do the "clean up" work necessary for a graceful shutdown physically.

---

## Part 4: Analyzing the Shutdown Code

The objective of this lesson is to view the web server code for the *Burger Queen* service to analyze how the service support the Disposability principle of 12 Factor App.

**NOTE:** Although the code for the *Burger Queen* service is written in Node.js, general way that graceful shutdown is accomplished can be implemented in any language.

## Steps

**Step 1:** Confirm you are in the working direcotyr for the *Burger Queen* service.

`cd /home/ubuntu/lab09/12factor/burgerqueen && pwd`{{execute T1}}

You should see the following output:

`//home/ubuntu/lab09/12factor/burgerqueen`

**Step 2:** Open the source code for the *Burger Queen* web server in `vi`.

`vi index.js`

**Step 3:** Turn on line numbering in the `vi` editor:

Press the ESC key: `^ESC`

and then enter:

`:set number`

Let's do a short analysis of code.


Notice the function, `shutdown` at `Lines 17 -33`. This function has the shutdown behavior for the web server. Notice that it sends out advisory messages via `console.log` reporting that server is shutting down. Then after the advisory information is transmitted, the actual `process.exit(0)` command is executed at `Line 28`.

Granted, the current behavior in the function, `shutdown` is trivial and we readily admit that using `console.log` is not the best way to log application data. Rather a logging framework that sends log data to stdout as well as central log collector should be used. In fact you'll learn about the importance of consistent, comprehensive logging when we look at the Logging principle of 12 Factor App.

But putting the anti-pattern of `console.log` aside for a moment, the function, `shutdown` does serve as a good example of centralizing web server shutdown o a place where more comprehensive termination behavior can be implemented.

**Step 5:** Let's examine the lines of code that capture the shutdown execution interruption signals coming in to the web server.

`:56`

This code is very specific to Node.js. The code is capturing execution interruption events using the the `process` object. This is happening at `Line 48` and `Line 52`.

The thing to keep in mind is that most, if not all, programming languages have a way to capture execution interruption signals. But, the way that execution interruption is captured can vary by programming language.

The important thing to understand that the code does indeed capture execution interruption and has a mechanism to respond with graceful termination of the service. This mechanism is the function, `shutdown`.

Remember, graceful termination is a key aspect of the Disposability principle of 12 Factor App.


**Step 6:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 7:** Exit `vi`

`:q!`

You have exited `vi`.

---

**Congratulations! You've completed the lab.**