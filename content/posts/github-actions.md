+++
title = "GitHub Action"
description = "Hugo, the world's fastest framework for building websites"
date = "2019-02-28"
aliases = ["about-us", "about-hugo", "contact"]
author = "Hugo Authors"
+++

The name of the topic is GitHub and Action. GitHub represents GitHub repository, whereas Action represents action that is performed after some activity( in technical terms known as events) by the user on the github repository. 
Let's understand with examples:

- Whenever someone pushes(activity or events) some commits to your repository, perform xyz action, or
- Whenever someone creates new issues(activity or events) in a repository, perform abc action, etc. 
- In short, users perform activities such as: push commits, create PR, create new issues ----> in repository ----> and based on those activities action is performed.

So, basically what we get out of examples is that the user performs some activity on the repository and according to those activities, corresponding actions are performed. Whatever action to be performed is  written as a workflow. And workflows are present under `.github/workflows`. These workflows totally depend on the type of activity performed. Because at the end activities or events will trigger workflow to run.
For different events or activities separate workflows are defined. For example, for push activity separate workflows is defined, for new issues separate workflows is defined. And it depends on the maintainer of the project what type of action is hooked corresponding to the event or activity. The workflows are written in Kubernetes native language i.e. YAML.

Now, let's understand GitHub Action in more `technical` terms.

- It is a CI/CD platform that allows you to automate build, test and deploy. If you are beginners and don't know about terms build, test, and deploy. For now, assumes these are bunch of commands which are wrapped for simplicity. 
Generally, build and test actions are performed for every PR activity. So, as the new PR created these bunch of commands in return are executed.  Whereas deploy actions are performed for every merged PR. Similarly, bunch of commands runs for deploy in return action.

## Components of GitHub Action

### Workflows

- Workflows represent action  executed automatically as it gets triggered by some kind of  event or activity performed by a user.
- Each workflow is defined for some activity or events.
Workflow is a configurable automated process that will run one or more jobs.
- Defined under .github/workflows. And written in YAML language.
- A repo can have multiple workflows for performing different sets of events.

### Events

- Activity performed by the user triggers workflows to be executed.
`Examples:`
- Create PR
- Create issues
- Push commits
- Release cycle
- Merge successful PRs

### Jobs

- Each workflow contains one or multiple jobs.
- And jobs contain one or more steps. 
- Step is script to be executed.
- Each step is executed inside the same runner.

### Action

Example:

- When a user creates a new issue(activity or events), it triggers a corresponding workflow. As a result, the label of the issue is automatically added. 

### Runner

- It is a server in which workflows run as they get triggered.
- Each runner runs a single job at a time.
- Each workflow is executed in fresh VMS or containers.

## Understanding the workflow file:

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6jsnux6d2us32kqbx8pe.png)

```
name: learn-github-actions
on: [push]
jobs:
  for-tutorial:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: echo "Hello, Setting up your first GA."
```

`name: learn-github-actions` → Name of the GitHub Action
`on: [push]` → Name of the event or activity. GitHub keeps eye on the activity perform on the repo. As the activity is performed by the user it will trigger to perform action or run the workflow file.  
`jobs:` → list of one or more actions
`check-bats-version:` → name of vary first job
`runs-on: ubuntu-latest` →  machine in which job will run.
`steps:` → list of one or more script or command.
- `uses: actions/checkout@v3`→ step 1 --> Pull the code base from github.
- `run: echo "Hello, Setting up your first GA."`  ---> Execute command/script on ubuntu machine.

Checkout this small [example](https://github.com/viveksahu26/news-app/blob/main/.github/workflows/testing-github-action.yml)

## Conclusion

- `GitHub Action is similar to the concept of Newtons Third Law of Motion. Every action by user on github repo by the user has opposite custom reaction.`

This was basic overview of GitHub Action for beginners. Hope you liked it. This was all from my side for this weekend. For more do follow for more interesting blogs.   

Comment below and let me know which topic you feel like complex, I will try my best to make it simple and interesting for you.
