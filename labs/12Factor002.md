# 12Factor Lab 2

## Objective

The objective of this lab is to demonstrate the basic concepts behind of the second of 12 Factor App principles, **[Dependencies](https://12factor.net/dependencies)**.

## What you'll be doing 

In this lab you'll doing following:

* **Part 1:** Setting Lesson Code
* **Part 2:** Understanding the Benefit of Dependencies
* **Part 3:** Managing Dependencies in Version Control

## Executing command line instructions 

All of the labs use Linux bash commands. If you are not familiar with the commands, ask your instructor for clarification;

## Using the VM

You will be logged into a Ubuntu VM machine. You should create a new directory in your home directory for each lab to avoid having the source code conflict with code from previous labs.

## About the demonstration application

This lab uses a demonstration application named, *Pinger*. The purpose of *Pinger* is to report environmental information of a webserver at runtime. We'll be adding features to *Pinger* throughout the labs in order to demonstrate the principles of 12 Factor App. In order to control development, a new branch will be created in this project's GitHub repository each time a feature set is created.

---

## Part 1: Setting Lesson Code
The objective of this lesson is demonstrate how to clone the lab code from GitHub into the Innovation In Software interactive learning environment.

## Steps

**Step 1:** Create a working directory from your home directory for the lab and locate to it:

`mkdir lab02`

`cd lab02`

**Step 2:** Get the code from GitHub:

`git clone https://github.com/innovationinsoftware/12factor.git`

**Step 3:** Navigate to the working directory of the code just cloned. This directory contains all the assets for the lab's demonstration application.

`cd 12factor`

You have cloned the code for this lab and have navigated to the working directory of the lab's demonstration application. 

---

## Part 2: Understanding the Benefit of Dependencies
The objective of this lesson is to demonstrate how use and manage external dependencies in order to make an applications flexible, scalable and easier to change.

In this lab we're going to compare two version of the lab's demonstration application to learn how control application change using dependency management.

## Key Concept: 12 Factor App - Dependencies
A key concept in the **Dependencies** principle of 12 Factor App is that all external modules, packages or libraries are stored independent of your application and are stored in an external artifact repository. You application will keep a list of of external dependencies and the development framework that you use to program your application will have the capability to download the required dependencies from the give artifact repository at runtime.

Under no circumstance should external dependencies be stored in the application's central repository.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab02/12factor`

**Step 2:** Check out the first version of *Pinger* from the local `git` repo that we've just cloned from GitHub

`git checkout 1-codebase.0.0.1`

You'll see output as follows:

```
Branch '1-codebase.0.0.1' set up to track remote branch '1-codebase.0.0.1' from 'origin'.
Switched to a new branch '1-codebase.0.0.1'

```
**Step 3:** Take a look at the contents of the demonstration application's working directory.

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

Notice that are **no** dependencies installed. If there were, under Node.js you would see directory named, `node_modules`.

However, the list of dependencies that the demonstration application needs is defined in the file, `package.json`.

**Step 4:** Let's take a look at the dependencies list in `package.json` using the `jq` tool.

`jq .dependencies package.json`

You'll see output as follows:

```
{
  "dotenv": "^8.2.0",
  "uuid": "^3.3.3"
}
```

The snippet of JSON shown above is the list of dependencies that applications needs. The dependencies  are Node.js packages that are stored in the Node.js package repository, [NPM](https://www.npmjs.com/). You use the command set, `npm install` to have the Node.js development framework go out to the Internet and install the dependencies from NPM, which we'll do in the following step.

**Step 4:** Install the demonstration application's dependencies.

`npm install`

You'll get the following output:

```
added 245 packages from 699 contributors and audited 245 packages in 5.274s

24 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

**Step 5:** Review the contents after the dependencies have been installed:

`tree ./ -L 1`

You'll get the following output:

```
./
├── node_modules
├── package.json
├── package-lock.json
├── readme.md
├── server.js
└── test

```

Notice that now the directory, `node_modules` is installed in the demonstration application's working directory.

**Step 6:** Take a look at the contents of the directory `node_modules`.

`ls ./node_modules/`

Notice that not only are the packages for `donenv` and `uuid` installed, but so are all the dependency packages that are associated with those two packages.

Having to manage each of these dependency installations manually is an arduous task. Fortunately following the **Dependendencies** principle of 12 Factor app makes things a lot easier.

## Discussion

The important thing to understand is that the **Dependencies** principle of 12 Factor App states that all external dependencies should exist in separate artifact repositories and downloaded at runtime. In this case, the default artifact repository for Node.js applications is [npmjs.com](https://www.npmjs.com/).

The immediate benefit is that the application code for *Pinger* contains only the logic that is absolutely necessary to support its features. Extraneous logic resides in external dependencies. This allows the application to be upgraded without having to accommodate a lot of unnecessary code. Also, using well defined external libraries means that an application can avoid the problem of [dependency hell](https://en.wikipedia.org/wiki/Dependency_hell).

---

## Part 3: Managing Dependencies in Version Control

The objective of this lesson is to demonstrate how use and manage external dependencies over multiple versions in order to make an applications flexible, scalable and easier to change.

In this lab we're going to compare two version of the lab's demonstration application to learn how control application change using dependency management.


## Steps

**Step 1:** Confirm you are in the working directory of the lab's application.

`pwd`

You should see the following output:

`/home/ubuntu/lab02/12factor`

If the working directory is not `/root/12factor`, execute the following command:

`cd /home/ubuntu/lab02/12factor`

**Step 2:** Let's take a look at the existing dependencies list in `package.json` using the `jq` tool

`jq .dependencies package.json`

You'll see output as follows:

```
{
  "dotenv": "^8.2.0",
  "uuid": "^3.3.3"
}
```

Notice that this version has only two packages listed. These are the only packages that this version of the code requires.

**Step 4:** Let's confirm that the dependencies we installed in the previous lesson are still in the working directory

`tree ./ -L 1`

You'll get the following output:

```
./
├── node_modules
├── package.json
├── package-lock.json
├── readme.md
├── server.js
└── test

```

**Step 5:** Start the demonstration application webserver in a second terminal window:

`cd lab02/12factor && node server.js`

You'll get the following output:

`API Server is listening on port 3030`

**Step 6:** Make a `curl` call to the application in the first terminal window:

`curl http://localhost:3030`

You'll get the following output:

```
{
    "appName": "Pinger",
    "currentTime": "2020-12-06T18:23:31.454Z",
    "PINGER_PORT": "3030"
}
```

Pay attention to the output. We'll be comparing it to the output in the second version.

In the window where the server is running, press control-C (ctl-c) to stop the server.

**Step 7:** Delete the `node_modules` directory that we installed previously

`rm -rf ./node_modules`

**Step 8:** Check out the second version of *Pinger* from the local `git` repo that we've just cloned from GitHub

`git checkout 2-dependencies.0.0.1`

You'll see output as follows:

```
Branch '2-dependencies.0.0.1' set up to track remote branch '2-dependencies.0.0.1' from 'origin'.
Switched to a new branch '2-dependencies.0.0.1'

```

**Step 9:** Now let's take a look at the dependencies list in the second version of `package.json` using the `jq` tool.

`jq .dependencies package.json`

You'll see output as follows:

```
{
  "dotenv": "^8.2.0",
  "faker": "^5.1.0",
  "uuid": "^3.3.3"
}
```

Notice the difference? You'll see that the package, [`faker`](https://www.npmjs.com/package/faker): "^5.1.0" has been added. Why has this addition been made?

The reason is because `faker` is needed to support a new feature of *Pinger*. This new feature will return a randomMessage as part of the HTTP response.

**Step 10:** Install the dependencies for the new, second version

`npm install`

**Step 11:** Start the web server in a second terminal window.

`node server.js`

You'll get the following output:

`API Server is listening on port 3030`

**Step 12:** Make a call the web server.

`curl http://localhost:3030`

You'll get the following output:

```
{
    "appName": "Pinger",
    "currentTime": "2020-12-06T18:24:51.495Z",
    "PINGER_PORT": "3030",
    "randomMessage": "totam inventore quis natus atque",
    "correlationId": "e45cc945-85eb-45fd-8f27-ae276d767178"
}
```

Notice that the applications response has an added attribute in the JSON, `randomMessage` as well as the attribute, `correlationId`. These are new attributes are special this version. Also, the value for the attribute, `randomMessage` is generated using the `faker` module has been added to the application as an independent dependency.

**Step 13:** Shutdown the server

In the window where the server is running, press control-C (ctl-c) to stop the server.

## Discussion

The important concept demonstrated in this lesson is that the demonstration application code contained only the logic with was relevant to its purpose and that it relied upon a dependency to provide the additional intelligence needed to realize the new feature.

Separating code from dependencies is an essential principle of 12 Factor App. Dependency separation makes it a lot easier to manage applications over the long run.

---

**Congratulations! You've finished the lab.**

