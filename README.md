> ⚠️ Alpha version, use for test purposes only

<p align="center">
  <a href="https://kairos.tools" target="_blank">
    <img src="https://raw.githubusercontent.com/snowcrack/kairos/main/logo.png" target="_blank" />
  </a><br>
  A time machine platform for modern applications.
  <a href="https://kairos.tools/help" target="_blank">
    <strong> Explore the docs » </strong>
  </a> <br>
  <a href="https://kairos.tools" target="_blank"> Website </a>
    ·
  <a href="https://kairos.tools" target="_blank"> Node sdk </a>
    ·
  <a href="https://kairos.tools" target="_blank"> Tutorial </a>
</p>
<p align="center">
  <a href="https://www.npmjs.com/package/zenaton"><img src="https://img.shields.io/npm/v/zenaton.svg" alt="NPM Version"></a>
  <a href="https://circleci.com/gh/zenaton/zenaton-node/tree/master" rel="nofollow" target="_blank"><img src="https://img.shields.io/circleci/project/github/zenaton/zenaton-node/master.svg" alt="CircleCI" style="max-width:100%;"></a>
  <a href="/LICENSE" target="_blank"><img src="https://img.shields.io/badge/iicense-MIT-blue.svg" alt="License" style="max-width:100%;"></a>
</p>

Note: This library can be used both as:

- a [sdk](https://docs.zenaton.com/client/nodejs-sdk/) to interact with workflows from your Node.js app
- a [sdk](https://docs.zenaton.com/workflows/implementation/) to build workflows.

# Zenaton library for Node.js

[Zenaton](https://zenaton.com) helps developers to easily build asynchronous workflows.

Build workflows using Zenaton functions to build control flows around your business logic and tasks - managing time, events and external services within one class. Functions include 'wait for a specific time or event', 'react to external events', 'run parallel tasks, 'create schedules' and more all by writing one line of code. [More about Zenaton Functions](https://zenaton.com/how-it-works)

Key capabilities:

- Standalone Tasks - dispatch or schedule an asynchronous business job with just one line of code.
- Workflows as code - Combine Zenaton functions and Node.js to create infinite possibilities of logic.
- Real-time Monitoring - Get a real-time view of workers and tasks - scheduled, processing and executed.
- Scheduler - Schedule recurrent tasks and workflows and automatically retry tasks that fail or get alerts when there are errors or timeouts.
- Error Handling: - Alerts for errors and timeouts and retry, resume or kill processes. React to errors by writing logic into workflow code to trigger retries or other actions. <br>

You can sign up for an account on [Zenaton](https://zenaton.com) and go through the [tutorial in Node](https://app.zenaton.com/tutorial/node/examples).

- [Changelog](CHANGELOG.md).

- [Breaking changes](BREAKINGCHANGES.md).

## Node Documentation

You can find all the details on [Zenaton's website](https://docs.zenaton.com).

## Requirements

Node 8 and later.

<details>
  <summary><strong>Table of contents</strong></summary>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Getting started](#getting-started)
  - [Installation](#installation)
    - [Install the Zenaton Agent](#install-the-zenaton-agent)
    - [Install the library](#install-the-library)
    - [Typescript typings](#typescript-typings)
  - [Quick start](#quick-start)
    - [Client Initialization](#client-initialization)
    - [Executing a background job](#executing-a-background-job)
  - [Orchestrating background jobs](#orchestrating-background-jobs)
    - [Using workflows](#using-workflows)
- [Getting help](#getting-help)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

</details>

## Getting started

### Installation

#### Install the Zenaton Agent

To install the Zenaton agent, run the following command:

```sh
curl https://install.zenaton.com/ | sh
```

#### Install the library

To add the latest version of the library to your project, run the following command:

```sh
npm install zenaton --save
```

#### TypeScript typings

For Typescript developers:

```bash
npm install @types/zenaton --save-dev
```

### Quick start

#### Client Initialization

To start, you need to initialize the client. To do this, you need your **Application ID** and **API Token**.
You can find both on [your Zenaton account](https://app.zenaton.com/api).

Then, initialize your Zenaton client:

```javascript
/* client.js */

const { Client } = require("zenaton");

module.exports = new Client(
  "<YourApplicationId>",
  "<YourApiToken>",
  "<YourApplicationEnv>" // Use "dev" as default
);
```

#### Boot file

The next step is to have your Zenaton Agent listen to your application.

The Agent needs to be pointed to a `boot` file which will allow it to figure out where tasks or workflows are located when the time comes to run them.

```javascript
/* boot.js */

const { task, workflow } = require("zenaton");

// define tasks (example)
task("hello_world_task", require("./tasks/HelloWorldTask"));

// define workflows (example)
workflow("my_first_workflow", require("./workflows/MyFirstWorkflow"));
```

To run the `listen` command:

```sh
zenaton listen --app_id=<YourApplicationId> --api_token=<YourApiToken> --app_env=<YourApplicationEnv> --boot=boot.js
```

#### Executing a background job

A job in Zenaton is created through the `task` function.

Let's start by implementing a first task printing something, and returning a value:

```javascript
/* tasks/HelloWorldTask.js */
module.exports.handle = async function(name = "World") {
  return `Hello ${name}`!;
};
```

Now, when you want to run this task as a background job, you need to do the following:

```javascript
/* launchHelloWorldTask.js */
const { run } = require("./client.js");

run.task("hello_world_task", "Me");
```

That's all you need to get started. With this, you can run many background jobs.
However, the real power of Zenaton is to be able to orchestrate these jobs. The next section will introduce you to job orchestration.

### Orchestrating background jobs

Job orchestration is what allows you to write complex business workflows simply.
You can execute jobs sequentially, in parallel, conditionally based on the result of a previous job,
and you can even use loops to repeat some tasks.

We wrote about some use-cases of job orchestration, you can take a look at [these articles](https://zenaton.com/https://zenaton.com/workflows-examples)
to see how people use job orchestration.

#### Using workflows

A workflow in Zenaton is created through the `workflow` function.

We will implement a very simple workflow that will execute sequentially the `hello_world_task` 3 times.

One important thing to remember is that your workflow implementation **must** be idempotent.
You can read more about that in our [documentation](https://zenaton.com/documentation/node/workflow-basics/#implementation).

The implementation looks like this:

```javascript
/* workflows/MyFirstWorkflow.js */
module.exports.handle = function* (name) {
  yield this.run.task("hello_world_task", name);
  yield this.run.task("hello_world_task", "Me");
  yield this.run.task("hello_world_task", "All");
};
```

Now that your workflow is implemented, you can ask for its processing like this:

```javascript
/* launchMyFirstWorkflow.js */
const { run } = require("./client.js");

run.workflow("my_first_workflow", "Gilles");
```

There are many more features usable in workflows to get the orchestration done right. You can learn more
in our [documentation](https://docs.zenaton.com).

## Getting help

**Need help**? Feel free to contact us by chat on [Zenaton](https://zenaton.com/).

**Found a bug?** You can open a [GitHub issue](https://github.com/zenaton/zenaton-node/issues).
