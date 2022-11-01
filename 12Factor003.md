# 12Factor Lab 3

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the third of 12 Factor App principles, **[Config](https://12factor.net/config)**.

## What is Config?

According to the website, [12 Factor App](https://12factor.net/config), Config is about storing configuration data in the environment.

*"Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.*

*A litmus test for whether an app has all config correctly factored out of the code is whether the codebase could be made open source at any moment, without compromising any credentials.*

*...this definition of 'config' does not include internal application config, such as `config/routes.rb` in Rails, or how code modules are connected in Spring. This type of config does not vary between deploys, and so is best done in the code."*

## What you'll be doing 

In this lab you'll doing following:

* **Part 1:** Cloning the Lesson Code from GitHub
* **Part 2:** Understanding the Benefit of an Independent Config
* **Part 3:** Managing Independent Config Across Versions
* **Part 4:** Changing Application Behavior Using Config Settings

### Executing command line instructions 

All of the labs use Linux bash commands. If you are not familiar with the commands, ask your instructor for clarification

## Using the VM

You will be logged into a Ubuntu VM machine. You should create a new directory in your home directory for each lab to avoid having the source code conflict with code from previous labs.

## About the demonstration application

This lab uses a demonstration application named, *Pinger*. The purpose of *Pinger* is to report environmental information of a webserver at runtime. We'll be adding features to *Pinger* throughout the labs in order to demonstrate the principles of 12 Factor App. In order to control development, a new branch will be created in this project's GitHub repository each time a feature set is created.

---
## Part 1: Getting the code
The objective of this lesson is demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab03`

`cd lab03`

**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 3:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

---

## Part 2: Understanding the Benefit of an Independent Config

The objective of this lesson is to demonstrate how use and manage external configuration according to the [Config](https://12factor.net/config) principle of 12 Factor App in order to make an applications flexible, scalable and easier to change.

In this lab we're going to compare two version of the lab's demonstration application to learn how control application change using external configuration.

## Key Concept: 12 Factor App - Dependencies
A key concept in the **Config** principle of 12 Factor App is that all external configuration data is stored independent of your application. Typically configuration information is injected into the environment at runtime. The actual information is stored in manifest files that are associated with the application but independent of it.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab03/12factor`

**Step 2:** Check out the an earlier version of *Pinger* from the local `git` repo that we've just cloned from GitHub

`git checkout 2-dependencies.0.0.1`

You'll see output as follows:

```
Branch '1-codebase.0.0.1' set up to track remote branch '2-dependencies.0.0.1' from 'origin'.
Switched to a new branch '2-dependencies.0.0.1'

```
**Step 3:** Take a look at the contents of the demonstration application's working directory.

`clear && ls -1Ap`

You'll see output as follows:

```
.env
.git/
package.json
readme.md
server.js
test/

```

Notice the file `.env`. This file contains environment settings that will be loaded into memory at run time. (Using a `.env` file is a simple approach that Node.js programmers use to load in environment information at runtime. Using `.env` assumes that the NPM package, [`dotenv`](https://www.npmjs.com/package/dotenv) is listed in `package.json` as a dependency.) 

**Step 4:** Let's at the contents of `.env` file.

`cat .env`

You'll see output as follows:

`PINGER_PORT=3030`

The environment variable `PINGER_PORT` defines the port on which the application's webserver will listen for incoming requests. (**NOTE** Using `PINGER_PORT` is special to the demonstration application.)


**Step 4:** Install the demonstration application's dependencies.

`npm install`

You'll get the following output:

```
added 245 packages from 699 contributors and audited 245 packages in 5.274s

24 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

**Step 5:** Let's start the applications webserver is second terminal window:

`cd lab03/12factor && node server.js`

You'll get the following output:

`API Server is listening on port 3030`

**Step 6:** In the first terminal window, let's send a request to the webserver and view the response.

`curl http://localhost:3030`


You'll get the following output:

```
{
    "appName": "Pinger",
    "currentTime": "2020-12-06T20:54:57.858Z",
    "PINGER_PORT": "3030",
    "randomMessage": "porro esse nesciunt qui doloribus",
    "correlationId": "ce4edf1b-6250-4665-be45-0e31a8780e06"
}

```

Notice that the webserver is listening on the [TCP/IP port](https://www.pcmag.com/encyclopedia/term/tcpip-port) 3030. The reason that the webserver is listening on port 3030 is because the application reads the value of the environment variable `PINGER_PORT` and runs on the port defined accordingly.

**Step 7:** Shut down the webserver 

In the window where the server is running, press control-C (ctl-c) to stop the server.

## Discussion

You can see in this lesson that the version of the demonstration application is determining which port to run the webserver on by reading from environmental variable, `PINGER_PORT`. This environment variable was injected at run time from the version's configuration file.

The important concept to understand is that we can associate configuration settings according to an application's version. And because the configuration settings are independent of the application they affect, we can alter them on a version by version basis. For example, we can change the port number of a web application without having to rewrite code. And, in some cases we can modify other behavior of the application by way of configuration changes.

Separating configuration setting from application code is an essential concept behind the **Config** principle of 12 Factor App.

In the next part we're going to checkout another version of the demonstration application from the common GitHub repository. This new version will run on a different port. Also, the new version will have additional application code that can be turned on and off according a configuration change.

The next part takes at look at how to manage configurations across multiple versions of an application based on the **Config** principle of 12 Factor App.

---

## Part 3: Managing Independent Config Across Versions

The objective of this lesson is to demonstrate how use and manage external configurations over multiple versions in order to make an applications flexible, scalable and easier to change.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab03/12factor`

If the working directory is not `/home/ubuntu/lab03/12factor`, execute the following command:

`cd /home/ubuntu/lab03/12factor`

**Step 2:** We're going to checkout another, later version of the demonstration application, *Pinger* . But, first we need to delete the existing directory, `node_modules` because the next version will have its own set of dependencies that need to downloaded and used at run time.

`clear && rm -rf node_modules`

**Step 3:** Check out a new version of the demonstration application from the local repository that you cloned down from GitHub at the start of this lesson.

`git checkout 3-config.0.0.1`

You'll see output as follows:

```
Branch '3-config.0.0.1' set up to track remote branch '3-config.0.0.1' from 'origin'.
Switched to a new branch '3-config.0.0.1'
```

**Step 4:** Let's take a look at the configuration settings in the `.env` file.

`cat .env`

You'll get the following output:

```
PINGER_PORT=3040
PINGER_ADMIN=true
CURRENT_VERSION="3-config.0.0.1"
```

Notice we've changed the port to which the webserver will listen for incoming requests. In the prior version as `3030`. Now, with the new configuration, the port is `3040`.

Notice too, that there a new environment variables in the `.env`. These environment variables are special to the demonstration application. They're meaning is as follows:

* `PINGER_PORT` defines the port where the application's webserver will be listening
* `PINGER_ADMIN` is a flag that when set to `true` will make it so that *Pinger* will return details about the environment in which it is running.
* `CURRENT_VERSION` describes the current version of the of *Pinger*

**Step 5:** Install the dependencies

`npm install`

**Step 6:** Start the webserver in a second terminal window:

`cd lab03/12factor && node server.js`

You'll get the following output:

`API Server is listening on port 3040`

**Step 7:** Make a `curl` call to the application in the first terminal window:

`curl http://localhost:3040`


Notice that now, we are getting a lot more information in the response and the configuration settings are affecting how the application behaves.

**Step 8:** Shut down the web server in preparation for next lesson

In the window where the server is running, press control-C (ctl-c) to stop the server.

In the next part we're going shut down the webserver and change the `PINGER_ADMIN` to `false` in order to change the application's behavior.

---

## Part 4: Changing Application Behavior Using Config Settings

The objective of this part is to demonstrate how change the demonstration application's behavior by changing configuration settings.

In the previous part we checked out a newer version of the *Pinger* demonstration application and looked at the impact of those changes.

In this part we're going change the existing configuration settings and then observe a directly a change in application behavior due to the changes made.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab03/12factor`

If the working directory is not `/home/ubuntu/lab03/12factor`, execute the following command:

`cd /home/ubuntu/lab03/12factor`

We're going change the configuration of the environment variables by deleting the existing `.env` file and replacing with the a different values. Changing the contents of `.env` file effectively changes the environment variables.

**Step 3:** Delete the `.env` file.

`rm -rf .env`

**Step 4:** Replenish the `.env` file with new values.

```
cat > .env<< EOF
PINGER_PORT=3040
PINGER_ADMIN=false
CURRENT_VERSION="3-config.0.0.1"
EOF
```

**Step 5:** Let's take a look at the configuration settings in the `.env` file.

`clear && cat .env`

You'll get the following output:

```
PINGER_PORT=3040
PINGER_ADMIN=false
CURRENT_VERSION="3-config.0.0.1
```
Notice we've changed the `PINGER_ADMIN` to `false`. This change will restrict the content of the response coming from the webserver.

**Step 6:** Start the demonstration application webserver in a second terminal window:

`cd /root/12factor && node server.js`

You'll get the following output:

`API Server is listening on port 3040`

**Step 7:** Make a `curl` call to the application in the first terminal window:

`curl http://localhost:3040`

You'll get output as follows:

```
{
    "appName": "Pinger",
    "currentTime": "2020-12-06T21:39:22.458Z",
    "PINGER_PORT": "3040",
    "randomMessage": "nulla tempore ipsum sit aut",
    "correlationId": "7bdb15cb-f2b1-428a-9c41-f2803595ccaa"
}
```

Notice that now, we are getting less information in the HTTP response. This is because we modified the configuration setting to `PINGER_ADMIN=false`. The demonstration application consumes the environmental variables. The application's logic is such that when it consumes, `PINGER_ADMIN=false`, the programming restricts the information sent in the response.

## Discussion

The important concept to understand in this lesson is that by separating configuration settings from application code as prescribed by 12 Factor App, we get a great deal of flexibility in terms of defining how an application will operate at runtime. Separating configuration settings from application code makes it so we can apply the core logic of application to a variety of runtime environments. Also, controlling configuration settings independent of the application makes continuous integration and continuous deployment (CI/CD) easier when moving an application through the various stages of release.

---

**Congratulations! You've complete the lab**

