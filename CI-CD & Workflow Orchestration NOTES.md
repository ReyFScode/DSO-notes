## The What and Why

CI/CD (Continuous Integration and Continuous Delivery/Deployment) is a modern software development practice that automates the process of building, testing, and deploying code. The purpose is to reduce manual overhead, improve code quality, and deliver updates more frequently and reliably.

Workflow orchestration is the process of coordinating tasks, dependencies, and tools in CI/CD pipelines to ensure automation flows consistently from commit to production.

This guide covers CI/CD implementations using GitHub Actions, GitLab CI/CD, Jenkins, Azure DevOps Pipelines, and includes CloudFormation for infrastructure automation.



# Pipeline tool 1 - Github Actions

## GitHub Actions Syntax and Examples:

### 1. Workflow Syntax:
A GitHub Actions workflow is defined using YAML syntax. Below is a basic structure for a workflow file:

```yaml
name: My Workflow  
--^ you must always start with a workflow name

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
--^ you must next define what event(s)/time(s) trigger the pipeline

permissions:
  contents: read  
--^ sets read permissions on the repo content

jobs:
  build-job: --specify job title
    runs-on: ubuntu-latest  
--^ build specifies the runner to use, you can use gitlab hosted runners or use your own
    
    steps:   -- steps for the job
    
      - name: Checkout code
        uses: actions/checkout@v4 
--^ you should pretty much always use this, it moves your repo code to the runner
        
      - name: update
        run: |
	      apt-get update -y
      - name: Test
        run: |
          echo "testing, testing, 123"
```

### 2. Events:
Events trigger workflows. Common events include `push`, `pull_request`, `schedule`, and `workflow_dispatch`. 

Here are explanations for each of the events listed above:

1. **push**:
   - This event occurs whenever a push is made to the repository.
   - It triggers the workflow whenever new commits are pushed to any branch or tag.
   - Typically used for continuous integration (CI) workflows to automatically build and test code changes.
```yaml
on:
  push:
    branches:
      - main

#You can also specify pushes for particular files, so if you only want to run the pipeline when a change to foo.txt is pushed you can use:

on:
  push:
    paths:
      - 'repo-directory/foo.txt'
```

2. **pull_request**:
   - This event occurs whenever a pull request is opened, updated, or synchronized (e.g., new commits are added to the pull request).
   - It triggers the workflow whenever pull requests are created or updated.
   - Commonly used for running automated tests and checks on pull requests to ensure code quality before merging.
``` YAML
on:
  pull_request:
    branches:
      - main
```

3. **schedule**:
   - This event allows you to trigger workflows at scheduled intervals, such as hourly, daily, weekly, etc.
   - It enables periodic tasks, such as backups, reports generation, or maintenance tasks, to be automated.
   - Useful for running routine tasks on a predefined schedule without manual intervention.
```yaml
on:
  schedule:
    - cron: '0 0 * * *'
```

4. **workflow_dispatch**:
   - This event allows manual triggering of workflows through the GitHub web interface or API.
   - It provides flexibility for users to execute workflows on-demand when needed.
   - Useful for tasks that require manual intervention or initiation, such as deployments or releases.

### 3. Jobs:
Jobs define the tasks to be executed in a workflow, they are used to define particular workflow stages like build, test. deploy, etc.. Each job runs in a separate environment. Jobs need a name, a runner (environment) to run-on, and steps to execute. Example:

```yaml
jobs:
  job1: #job name
    runs-on: ubuntu-latest #runner
    steps:  #steps that comprise the job
      - name: Checkout code / step-1
        uses: actions/checkout@v2
      - name: job1-step-2
        uses: actions/checkout@v2
  job2:
    runs-on: ubuntu-latest
    steps:
      - name: job2-step-1
        uses: actions/checkout@v2
```

### 4. Steps:
Steps are individual tasks within a job. They can be predefined actions or custom commands. Example (a checkout task to move code to the runner, an echo step to echo some text, and a step that uses a prebuilt action to do a security scan for exposed secrets ):

```yaml
steps:

  - name: Checkout-step
    uses: actions/checkout@v4
--^ you should pretty much always use this step, it moves your repo code to the runner

  - name: echo-step
    run: |
      echo "hello-world"
      
  - name: Secret-Scanning-step
    uses: trufflesecurity/trufflehog@main
      with:
        extra_args: --only-verified

```
Github has a large amount of prebuilt actions from providers (users/organizations), you specify them with the `uses` keyword, you can view them here: [GitHub Marketplace · Actions to improve your workflow](https://github.com/marketplace?type=actions)

### 5. Actions:
Actions are reusable units of code that perform a specific task. They are the steps that comprise jobs, example:

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v2
  - name: Build
    uses: actions/setup-node@v2
    with:
      node-version: '14'
  # Add more steps using actions
```

### 6. Environment Variables:
You can define environment variables for jobs or steps, you can also define global vars to be used by all jobs and steps.

```yaml
name: Variable Example Workflow

# Define global environment variables
env:
  GLOBAL_VAR: 'Global Value'

jobs:
  build:
    runs-on: ubuntu-latest
    
    # Define job-level environment variables
    env:
      JOB_VAR: 'Job Value'
    
    steps:
      # Define step-level environment variables
      - name: Set Step Variable
        env:
          STEP_VAR: 'Step Value'
		run: | 
			echo "Global Variable: $GLOBAL_VAR" 
			echo "Job Variable: $JOB_VAR" 
		    echo "Step Variable: $STEP_VAR"
```
you can access variables using one of two formats: 
`$foo` or `${{ env.foo }}`
If you set vars in the settings (available to all actions) you can access them with `${{ vars.foo }}`

### 7. Conditional Execution:
You can conditionally execute steps based on the outcome of previous steps. Example:

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v2
  - name: check_something
    run: |
      ls -al | grep -io "somefile"
  - name: Test
    if: success()
    run: |
      echo "success"
```
reference: [Using conditions to control job execution - GitHub Docs](https://docs.github.com/en/actions/using-jobs/using-conditions-to-control-job-execution)

You can also register the output of steps to control the flow, reference this doc: [Defining outputs for jobs - GitHub Docs](https://docs.github.com/en/actions/using-jobs/defining-outputs-for-jobs)

### 8. GitHub runners:
GitHub Actions workflows (liked other CI/CD offerings) are executed on machines known as runners. Runners provide the environment in which your workflow jobs run and execute the defined steps. There are two types of runners available in GitHub Actions: Hosted and Self-hosted.

When you register self-hosted runners you must specify a tag for the runner you use the tag to assign-to jobs like this:
```yaml
jobs: 
  build: 
    runs-on: self-hosted 
    labels: [tag1, tag2, tag3]
```

Runner documentation: [Choosing the runner for a job - GitHub Docs](https://docs.github.com/en/actions/using-jobs/choosing-the-runner-for-a-job)

### 9. Secrets:
You should never hardcode access keys, passwords, or other sensitive information into your workflows, instead we use repository secrets to manage our workflows access to the aforementioned sensitive information. To create secrets you go to repository settings, secrets and variables, and choose the actions section, here we enter our secrets

To utilize secrets within our workflow we reference them with this syntax: `${{ secrets.SECRET_NAME }}`

#ADD info on artifacts- [Storing workflow data as artifacts - GitHub Docs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

#ADD info on dependencies
In GitLab CI/CD, the equivalent concept to dependencies in GitHub Actions is using the `needs` keyword. 
 12. Dependencies (Equivalent: `needs`):
In GitLab CI/CD, you can define dependencies between jobs using the `needs` keyword. This ensures that certain jobs only start after the successful completion of other jobs. This is similar to the dependencies feature in GitHub Actions.

Here's an example of how to define dependencies using `needs`:

```yaml
test-job:
  stage: test
  script:
    - echo "Running tests..."

deploy-job:
  stage: deploy
  script:
    - echo "Deploying the application..."
  needs:
    - test-job
```

In this example, the `deploy-job` has a dependency on the `test-job`. This means that the `deploy-job` will only start after the successful completion of the `test-job`. If the `test-job` fails, the `deploy-job` will not run.

Using the `needs` keyword allows you to control the order of job execution and ensure that certain jobs only run when their dependencies have been successfully completed, similar to how dependencies work in GitHub Actions.



---
# Provider 2 - Gitlab CI/CD

## GitLab CI/CD Syntax and Examples:

### 1. .gitlab-ci.yml File Structure:
A GitLab CI/CD pipeline is defined using YAML syntax. Below is a basic structure for a .gitlab-ci.yml file:

```yaml
stages: 
  - build
  - test
  - deploy
```

### **2. Triggers:**

GitLab CI/CD pipelines can be triggered by various events, including code pushes, merge requests, schedules, and manual interactions.

###### 1. **Push Events**:

- Pipelines are triggered automatically when code is pushed to specified branches.
    
- Common for CI (continuous integration) to run tests or builds.
    

```yaml
# .gitlab-ci.yml
job_name:
  script: echo "Running on push to main"
  only:
    - main
```

Or using `rules` for more flexibility:

```yaml
job_name:
  script: echo "Triggered on push"
  rules:
    - if: '$CI_PIPELINE_SOURCE == "push"'
      when: always
```

###### 2. **Merge Requests**:

- Triggered when a merge request is created or updated.
    
- Useful for validating changes before they’re merged.
    

```yaml
job_name:
  script: echo "Running on merge request"
  only:
    - merge_requests
```

Or with `rules`:

```yaml
job_name:
  script: echo "Running on merge request"
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      when: always
```


### 3. Stages:
Stages in GitLab CI/CD allow you to organize and visualize the execution of jobs in your pipeline. Each stage represents a distinct phase in your pipeline workflow, such as build, test, deploy, etc. Jobs within the same stage run concurrently, while subsequent stages wait for the completion of previous stages before starting. Example:

```yaml
stages:
  - build
  - test
  - deploy
```

By defining stages in your pipeline configuration, you can achieve better control over the order in which jobs are executed and visualize the progression of your pipeline workflow in GitLab's pipeline graph.

Here's how you can assign jobs to stages:

```yaml
build-job:
  stage: build
  script:
    - echo "Building the project..."

test-job:
  stage: test
  script:
    - echo "Running tests..."

deploy-job:
  stage: deploy
  script:
    - echo "Deploying the application..."
```

In this example, the `build-job` runs in the `build` stage, the `test-job` runs in the `test` stage, and the `deploy-job` runs in the `deploy` stage. Jobs within the same stage will run concurrently, while jobs in subsequent stages will wait for the completion of previous stages before starting.

Using stages provides better organization, improves pipeline visualization, and ensures a logical flow of execution in your GitLab CI/CD pipeline.

### 4. Jobs:
Jobs define the tasks to be executed in a pipeline, they are used to define particular pipeline stages like build, test, deploy, etc. Each job runs in a separate environment. Jobs need a name, and can have stages, script, or predefined actions to execute. Example:

```yaml
build-job: 
  stage: build
  script: 
    - echo "Building the project..."
```

### 5. Steps:
Steps are individual tasks within a job. They can be predefined commands, scripts, or Docker images. Example:

```yaml
build-job: 
  stage: build
  script: 
    - echo "Building the project..."
    - npm install
```

### 6. Artifacts:
Artifacts are the files generated by the jobs which can be passed between jobs or downloaded as a build result. Example:

```yaml
build-job: 
  stage: build
  script: 
    - echo "Building the project..."
  artifacts:
    paths:
      - dist/
```

### 7. Dependencies:
Jobs can define dependencies on other jobs so that they only run when the dependent jobs succeed. Example:

```yaml
test-job: 
  stage: test
  script: 
    - echo "Running tests..."
  dependencies:
    - build-job
```

### 8. Variables:
You can define environment variables for jobs or globally in GitLab CI/CD settings. Example:

```yaml
variables:
  GLOBAL_VAR: "Global Value"

build-job: 
  stage: build
  script: 
    - echo "Building the project..."
    - echo $GLOBAL_VAR
```

### 9. Conditional Execution:
You can conditionally execute steps based on the outcome of previous steps. Example:

```yaml
test-job: 
  stage: test
  script: 
    - echo "Running tests..."
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

### 10. Runners:
GitLab CI/CD pipelines are executed on machines known as runners. Runners provide the environment in which your pipeline jobs run and execute the defined steps. Runners can be shared or specific to your project.

### 11. Secrets:
You should avoid hardcoding sensitive information into your pipelines. Instead, use GitLab CI/CD variables or secret variables to securely manage sensitive data.



---
# Provider 3 - Jenkins

### Jenkins CI/CD 101: An Intermediate Overview

#### Introduction to CI/CD
Continuous Integration (CI) and Continuous Deployment (CD) are practices that automate the software development lifecycle, allowing teams to deliver code changes more frequently and reliably. Jenkins is one of the most popular open-source tools that supports CI/CD practices.

#### Key Concepts

1. **Jenkins Basics**
   - **Jenkins Server**: The central hub where the CI/CD pipeline runs.
   - **Jenkins Nodes**: Machines that run jobs; can be master/slave architecture.
   - **Pipeline**: A sequence of automated steps (stages) that define the build, test, and deployment process.

2. **Plugins**
   - Jenkins has a rich ecosystem of plugins to extend its functionality. Examples include:
     - **Git Plugin**: Integrates with Git repositories.
     - **Docker Plugin**: Facilitates building and deploying Docker images.
     - **Pipeline Plugin**: Allows the creation of pipelines as code.

3. **Jenkinsfile**
   - A text file that defines a Jenkins pipeline using a domain-specific language (DSL). This file can be versioned with your source code.
   - Example structure:
     ```groovy
     pipeline {
         agent any
         stages {
             stage('Build') {
                 steps {
                     sh 'make'
                 }
             }
             stage('Test') {
                 steps {
                     sh 'make test'
                 }
             }
             stage('Deploy') {
                 steps {
                     deployToProduction()
                 }
             }
         }
     }
     ```

#### Building a Pipeline

1. **Setting Up Jenkins**
   - Install Jenkins on a server (local or cloud-based).
   - Configure global settings like JDK, Git, and other necessary tools.

2. **Creating a New Job**
   - Use the "New Item" feature to create a Freestyle project or Pipeline.
   - For a Pipeline project, specify the location of the `Jenkinsfile` in your repository.

3. **Defining Stages**
   - **Build Stage**: Compile the code, generate artifacts (e.g., JAR, WAR files).
   - **Test Stage**: Run unit tests and integration tests. Use tools like JUnit or Selenium.
   - **Deploy Stage**: Deploy artifacts to production or staging environments. Integrate with tools like Kubernetes or AWS.

#### Advanced Concepts

1. **Parameterized Builds**
   - Allow users to input parameters at runtime, enabling more flexible builds. For example, you might want to deploy to different environments based on user input.

2. **Webhook Integration**
   - Set up webhooks in your version control system (e.g., GitHub, GitLab) to trigger builds automatically when code is pushed.

3. **Post-Build Actions**
   - Define actions that occur after the build completes, such as sending notifications (Slack, email), archiving artifacts, or triggering downstream jobs.

4. **Environment Variables**
   - Utilize Jenkins environment variables to configure builds dynamically. You can use the `env` object within the Jenkinsfile to access these variables.

5. **Pipeline as Code**
   - Store `Jenkinsfile` in the version control system to enable versioning of your CI/CD pipeline. This practice ensures consistency across different environments.

6. **Declarative vs. Scripted Pipelines**
   - **Declarative Pipelines**: More structured and user-friendly syntax.
   - **Scripted Pipelines**: Offers more flexibility but requires a deeper understanding of Groovy.

#### Best Practices

1. **Version Control Everything**
   - Keep your `Jenkinsfile` and any configuration scripts in version control to track changes and facilitate collaboration.

2. **Use Shared Libraries**
   - Create reusable components or functions that can be shared across multiple pipelines to reduce duplication.

3. **Monitor and Optimize**
   - Monitor the performance of your pipelines and optimize them by identifying bottlenecks. Use Jenkins monitoring plugins for insights.

4. **Security Considerations**
   - Regularly update Jenkins and plugins to patch vulnerabilities.
   - Use credentials binding to manage sensitive information securely.




---







---
---
---
# CI/CD & Workflow Orchestration Guide

## The What and Why

CI/CD (Continuous Integration and Continuous Delivery/Deployment) is a modern software development practice that automates the process of building, testing, and deploying code. The purpose is to reduce manual overhead, improve code quality, and deliver updates more frequently and reliably.

Workflow orchestration is the process of coordinating tasks, dependencies, and tools in CI/CD pipelines to ensure automation flows consistently from commit to production.

This guide covers CI/CD implementations using GitHub Actions, GitLab CI/CD, Jenkins, Azure DevOps Pipelines, and includes CloudFormation for infrastructure automation.

## GitHub Actions Overview

GitHub Actions allows you to automate workflows based on GitHub repository events. Workflows are defined in `.github/workflows/*.yml` files.

### Syntax Basics

```yaml
name: My Workflow
on:
  push:
    branches: [main]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run a script
        run: echo "Hello World"
```

### Key Components

1. **Events**: Trigger workflows (e.g., `push`, `pull_request`, `schedule`, `workflow_dispatch`).
    
2. **Jobs**: Logical groups of steps. Each job runs in a fresh virtual environment.
    
3. **Steps**: Individual tasks inside a job (e.g., `run`, `uses`).
    
4. **Runners**: Machines where jobs run (GitHub-hosted or self-hosted).
    
5. **Actions**: Reusable steps defined in external repos (e.g., `actions/checkout`).
    
6. **Environment Variables**: Defined at workflow, job, or step level.
    
7. **Secrets**: Stored securely in GitHub and used in workflows via `${{ secrets.MY_SECRET }}`.
    
8. **Artifacts**: Output files you can pass between jobs or download.
    
9. **Dependencies**: Control job order using `needs`.
    

### Example: Python App

```yaml
name: Python CI
on:
  push:
    branches: [main]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - run: |
          pip install -r requirements.txt
          python main.py
      - run: pytest
```

---

## GitLab CI/CD Overview

GitLab uses a `.gitlab-ci.yml` file to define pipelines.

### Structure

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  image: python:3.10
  script:
    - pip install -r requirements.txt
    - python main.py

test-job:
  stage: test
  image: python:3.10
  script:
    - pip install -r requirements.txt
    - pytest
```

### Key Concepts

1. **Stages**: Ordered execution phases (e.g., `build`, `test`, `deploy`).
    
2. **Jobs**: Individual tasks with scripts.
    
3. **Triggers**: Pipelines run on events like `push`, `merge_request`, `schedule`.
    
4. **Variables**: Defined globally or per job.
    
5. **Artifacts**: Files saved between stages.
    
6. **Dependencies**: Job-to-job dependencies using `needs`.
    
7. **Runners**: Machines executing jobs (shared or project-specific).
    
8. **Secrets**: Managed through GitLab CI/CD variables.
    

---

## Jenkins Overview

Jenkins is a flexible, plugin-driven CI/CD tool.

### Jenkinsfile Example

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'make'
      }
    }
    stage('Test') {
      steps {
        sh 'make test'
      }
    }
    stage('Deploy') {
      steps {
        sh './deploy.sh'
      }
    }
  }
}
```

### Features

1. **Plugins**: Extend Jenkins (e.g., Git, Docker, Slack).
    
2. **Declarative Pipelines**: Simple and readable pipeline definitions.
    
3. **Scripted Pipelines**: More flexible, complex pipelines.
    
4. **Shared Libraries**: Reuse logic across multiple pipelines.
    
5. **Environment Variables & Parameters**: Configure builds dynamically.
    
6. **Post-Build Actions**: Notifications, archiving, chaining jobs.
    

---

## Azure DevOps Pipelines Overview

Azure DevOps Pipelines are defined in YAML files (`azure-pipelines.yml`) and can automate CI/CD across Azure and other platforms.

### Example Pipeline

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  pythonVersion: '3.10'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '$(pythonVersion)'

- script: |
    pip install -r requirements.txt
    python main.py
  displayName: 'Run application'

- script: pytest
  displayName: 'Run tests'
```

### Key Concepts

1. **Triggers**: Based on `push`, PRs, schedules.
    
2. **Jobs and Steps**: Similar to GitHub Actions.
    
3. **Tasks**: Built-in or custom steps.
    
4. **Pools**: Define execution environments.
    
5. **Artifacts**: Share results between jobs.
    
6. **Variables and Secrets**: Managed in pipeline settings or code.
    
7. **Service Connections**: Securely connect to cloud providers.
    

---

## CloudFormation for IaC

CloudFormation automates provisioning of AWS infrastructure.

### Basic Template Structure

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Simple EC2 Example
Resources:
  MyInstance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0abcdef1234567890
```

### Key Concepts

1. **Templates**: Declarative definition of AWS resources.
    
2. **Stacks**: Deployed instances of templates.
    
3. **Parameters**: Input values for flexible deployments.
    
4. **Outputs**: Expose stack values (e.g., IP address).
    
5. **Change Sets**: Review changes before deployment.
    
6. **Nested Stacks**: Compose large systems modularly.
    

CloudFormation works well when integrated into CI/CD workflows to provision infrastructure before deploying applications.

---

Let me know if you'd like additional provider examples (e.g., CircleCI, Bitbucket Pipelines) or a printable comparison table.