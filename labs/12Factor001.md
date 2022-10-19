
# 12Factor Lab 1

## Objective

The objective of this lab is to demonstrate the basic concepts behind the first of 12 Factor App principles, **[Codebase](https://12factor.net/codebase)**.

## What you'll be doing 

In this lab you'll do the following:

* **Part 1:** Getting the Lab's Code from GitHub
* **Part 2:** Inspecting the General Codebase
* **Part 3:** Installing Dependencies
* **Part 4:** Viewing the Application Code
* **Part 5:** Viewing the Application's Configuration File
* **Part 6:** Running the Application's Tests
* **Part 7:** Running the Application

## Executing command line instructions 

All of the labs use Linux bash commands. If you are not familiar with the commands, ask your instructor for clarification

## Using the VM

You will be logged into a Ubuntu VM machine. You should create a new directory in your home directory for each lab to avoid having the source code conflict with code from previous labs.

## About the demonstration application

This lab uses a demonstration application named, *Pinger*. The purpose of *Pinger* is to report environmental information of a webserver at runtime. We'll be adding features to *Pinger* throughout the labs in order to demonstrate the principles of 12 Factor App. In order to control developement, a new branch will be created in this project's GitHub repository each time a feature set is created.

---
## Part 1: Getting the code
The objective of this lesson is demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab01`

`cd lab01`

**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 3:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

---

## Part 2:  Inspecting the General Codebase
The objective of this lesson is inspect the various files in the lab's codebase in order to get a sense of how all assets that make up the application are stored in the common repository and versioned according to GitHub branches.

## Key Concept: 12 Factor App - Codebase
A key concept in the first principle of 12 Factor App is that all the assets of an application are stored in a common source code repository. Assets include not only the code logic that makes up the application but all configuration information relevant to a given version, a list of dependencies that the main application requires, as well as any tests that are to be run against the application.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab01/12factor`

**Step 2:** Checkout the code from the GitHub branch for the version of the application code for this lab, in this case `1-codebase.0.0.1`.

`git checkout 1-codebase.0.0.1`

You'll see output as follows:

```
Branch '1-codebase.0.0.1' set up to track remote branch '1-codebase.0.0.1' from 'origin'.
Switched to a new branch '1-codebase.0.0.1'
```

**Step 3:** View the files in the branch:

`tree ./`

You'll see output as follows:

```
./
├── package.json
├── readme.md
├── server.js
└── test
    └── http-tests.js

1 directory, 4 files

```
**WHERE**

* `package.json` contains the list of external dependencies for the application.
* `readme.md` is the introductory readme documentation for the application.
* `server.js` is the application code, which has logic written in Node.JS and runs as an HTTP webserver.
* `test` is the directory that has files that contains code for testing the application.

**Step 4:** Also, there is a hidden file named, `.env` that contains environmental configuration setting(s) that the application will use. Let's confirm the file is there: 

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

Notice the `.env` file in the list. This file holds configuration information that will be used by the application. We'll demonstrate different aspects of configuration as they apply to 12 Factor App throughout the labs. The important thing to know for now is that the 12 Factor App configuration information is stored in the common repository according to the version of the code to which the configuration applies.

---

## Part 3:  Installing Dependencies
The objective of this lesson is demonstrate how to view and install the external dependencies that the lab's application requires.

## Key Concept: 12 Factor App - Dependencies
A key concept in the second principle of the 12 Factor App is that all independent code modules and libraries are listed as dependencies of the application and installed when the main application needs them at runtime, either for testing or general operation.

We're going to cover Dependencies in-depth in upcoming labs, but for now, the important thing to understand is that the lab's application has external dependencies, and they will be installed now.

## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab01/12factor`

**Step 2:** Confirm you are in correct GitHub Branch

`git branch`

You should see the following output:

`1-codebase.0.0.1`

**Step 3:** View the file, `package.json` which is the list of Node.Js dependencies the application requires.

`clear && cat package.json`

---

The list of dependencies is shown in this snippet of JSON.

```
  "devDependencies": {
    "chai": "^4.2.0",
    "mocha": "^6.2.0",
    "nyc": "^14.1.1",
    "supertest": "^4.0.2",
    "globby": "^10.0.1",
    "lnk": "^1.1.0"
  },
  "dependencies": {
    "dotenv": "^8.2.0",
    "uuid": "^3.3.3"
  }
```

**Step 4:** Install the dependencies

`npm install`

When installation of the dependencies is complete you will see output as follows:

```
added 245 packages from 699 contributors and audited 245 packages in 6.453s

24 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```
You are now ready to view and analyze the application code.

---

## Part 4: Viewing the Application Code
The objective of this lesson is to examine code for the lab's demonstration application. 

The demonstration application is named, *Pinger*. The purpose of Pinger is to report information about the environment in which it is running. Pinger runs as a webserver.

Pinger is not very useful in its current version. We're going to "grow" Pinger over the course of a few labs as we demonstrate each of the twelve principles of 12 Factor App.

## Steps to Access the Application Code


**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd` 

You should see the following output:

`/home/ubuntu/lab01/12factor`

If you are not in that working directory, execute the following command: `cd /home/ubuntu/lab01/12factor`

**Step 2:** Open the application code in the `vi` editor.

`vi server.js`

**Step 3:** Turn on line numbering in `vi`.

Press the ESC key: `^ESC`

and then enter:

`:set number`


You will see current version of the Pinger code, which is written in Node.JS, in the `vi` editor with line numbers showing.

## Discussion of Application

This code for the demonstration application is Javascript that runs under [Node.js](https://nodejs.org/en/about/).

The code at lines 1 - 3 binds the external dependencies to the application code. The dependencies are Node.js packages which are libraries that contain logic that is intended to be shared among a variety of applications in a general manner. For example, the package, `dotenv` is a libary that reads the contents of a special file name `.env` to create environment variables that need be in memory and which the application will use.

The package `http` contains functions for creating and running a webserver. The package `uuid/v4` contains functions to generate random [universally unique identifiers](https://en.wikipedia.org/wiki/Universally_unique_identifier). 

([Dependencies](https://12factor.net/dependencies) is the second principle of 12 Factor App which we'll discuss in upcoming labs.)

Line 4 declares variable, `port`. The value of `port` is the [TCP/IP](https://www.pcmag.com/encyclopedia/term/tcpip-port) port on which the web server will listen for incoming requests. If there is an environment variable with the name `PINGER_PORT` defined in the runtime environment, that value will be used. Otherwise, the default value, `3000` will be assigned to the variable, `port`. 

Lines 6 -  16 is the Javascript function, `handleRequest` that accepts the HTTP `request` coming in from the internet. The function creates a simple HTTP `response` that returns a JSON object that reports the name of the application as well as the current time.

Line 18 is the intelligence that creates the actual web server using the function, [`http.createServer`](https://nodejs.org/dist/latest-v14.x/docs/api/http.html#http_http_createserver_options_requestlistener). The request handler function, `handleRequest` is passed to `createServer()` as a parameter.

The server binds to the port at Lines 20 - 22 in the function, `server.listen()`

Lines 24 -27 defined a function, `shutdown()`. The purpose of `shutdown()` is to gracefully stop the server from running. The notion of a graceful shut down is part of the ninth principle of 12 Factor App: [Disposability](https://12factor.net/disposability). We'll discuss Disposability in upcoming labs.

## Steps to Exit the Application Code

**Step 4:** Get out of `vi` line numbered view mode

Press the ESC key: `^ESC`

**Step 5:** Exit `vi`

`:q!`

You have exited `vi`.

Now that you have a basic understanding of the code for the lab's demonstration application, we'll move on and look at how the runtime environment variable `PINGER_PORT` is set in the configuration file.

---


## Part 5:  Viewing the Application's Configuration File
The objective of this lesson is learn about how to provide application configuration information as part of an application's common code base in accordance with the Configuration princple of The 12 Factor App. 


## Key Concept: 12 Factor App - Configuration
A key concept in the third principle of 12 Factor App is configuration for the application is stored in along with the application in the common repository.

We're going to cover configuration in depth in upcoming labs, but for now the important thing to understand is that the configuration information for the lab's application is stored in the general codebase according to a version branch.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab01/12factor`

If you are not in that working directory, execute the following command: `cd /home/ubuntu/lab01/12factor`

**Step 2:** Confirm you are in correct GitHub Branch

`git branch`

You should see the following output:

`1-codebase.0.0.1`

**Step 3:** Also, let's look at the hidden configuration file. First, we'll confirm the file is there: 

`clear && ls -1Ap`

Notice the `.env` file in the list. This file holds configuration information that will be used by the application.

**Step 5:** Now, look at the contents

`cat .env`

You'll see the following:

`PINGER_PORT=3030`

This configuration setting defines an environment variable, `PINGER_PORT` and assigns the value `3030` to the variable. There is programming logic that is special to the application that reads the value of `PINGER_PORT` in order to determine the port that the application's web server will listen on for incoming requests.

Now that we've reviewed how an external configuration file is used to set an environmental variable that gets consumed by the demonstration application, we'll move on to testing the application using the tests that are part of the general codebase.

**Remember!** A central idea of CodeBase principle in 12 Factor App is that all assets for the application are stored in one central repository, this includes configuration information as well as application tests.

---

## Part 6:  Running the Unit Tests
The objective of this lesson is demonstrate how to execute the tests that ship with the lab's demonstration application as part of the code download from GitHub.

**Remember!** A key concept in the Codebase principle of 12 Factor App is that all assets relevant to an application are stored in the application's central repository. This includes application tests. The demonstration application, *Pinger* contains its tests.

## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab01/12factor`

If you are not in that working directory, execute the following command: `cd /home/ubuntu/lab01/12factor`

**Step 2:** Run the tests that ship with the code.

`npm test`

Notice the tests of run. Two tests have passed as shown in the line:

`2 passing (31ms)`

Also notice that running the tests produced a [code coverage](https://www.techopedia.com/definition/22535/code-coverage) report, like so:

```

-----------|----------|----------|----------|----------|-------------------|
File       |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
-----------|----------|----------|----------|----------|-------------------|
All files  |      100 |       50 |      100 |      100 |                   |
 server.js |      100 |       50 |      100 |      100 |                 4 |
-----------|----------|----------|----------|----------|-------------------|

```

A coverage report describes how many lines of code were actually exercised in the testing session. While not part of 12 Factor App, the notion of testing all lines of code is an important concept in testing best practices. Unfortunately the test we've just run has not exercised 4 lines of code.

---

We've tested the application to verity that the code is operational. The next lesson is the get the application up and running.


## Part 7:  Running the Application
The objective of this lesson is to demonstrate how to start the lab's demonstration application's webserver and then call the server using the `curl` command.

## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab01/12factor`

If you are not in that working directory, execute the following command: `cd /home/ubuntu/lab01/12factor`.

**Step 2:** Start the Pinger web server

`node server.js`

You will see the following output:

`API Server is listening on port 3030`


**Step 3:** In a second terminal window call the web server using the `curl` command

`curl http://localhost:3030`

You will see the following output:

```
{
    "appName": "Pinger",
    "currentTime": "2020-12-24T16:04:38.215Z",
    "PINGER_PORT": "3030"
}
```

Notice that the webserver is running on the port defined by environment variable, `PINGER_PORT` as declared in the configuration file, `.env`. 

**Step 4:** Shutdown the server

In the first window where the server is running, press control-C (ctl-c) to stop the server.

As mentioned at the beginning of the lab, the **Codebase** principle of 12 Factor App is that all assets relevant to the application are stored along with the application code in a central repository. We'll look at the details of storing configuration information in the central repository in the lab that demonstrates the third principle of 12 Factor App: Configuration.

---

***CONGRATULATIONS!! You've finished the lab!***

