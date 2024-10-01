
# CI/CD NOTES

CI/CD stands for Continuous Integration/Continuous Deployment. It's a software development practice where code changes are automatically built, tested, and deployed. CI ensures that code changes are integrated into the main codebase regularly and tested automatically, while CD automates the deployment of code changes to production environments.

In this document we cover three of the major CI/CD providers/tools that you can use to create CI/CD workflows, Github Actions, Gitlab CI/CD, and Jenkins.

---

# Provider 1 - Github Actions

Certainly! Here's a document that covers basic to intermediate syntax and examples for GitHub Actions:

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

### 2. Triggers:
GitLab CI/CD pipelines can be triggered by various events including code pushes, merge requests, schedules, and manual triggers.

1. **Push Events**:
   - These events occur whenever a push is made to the repository.
   - It triggers the pipeline whenever new commits are pushed to any branch or tag.
   - Typically used for continuous integration (CI) workflows to automatically build and test code changes.
```yaml
# Trigger pipeline on push events
on:
  push:
    branches:
      - main
```

2. **Merge Requests**:
   - These events occur whenever a merge request is opened, updated, or synchronized (e.g., new commits are added to the merge request).
   - It triggers the pipeline whenever merge requests are created or updated.
   - Commonly used for running automated tests and checks on merge requests to ensure code quality before merging.
```yaml
# Trigger pipeline on merge requests
on:
  merge_request:
    branches:
      - main
```

3. **Schedules**:
   - Schedules allow you to trigger pipelines at scheduled intervals, such as hourly, daily, weekly, etc.
   - Enables periodic tasks, such as backups, reports generation, or maintenance tasks, to be automated.
   - Useful for running routine tasks on a predefined schedule without manual intervention.
```yaml
# Trigger pipeline on schedule
schedules:
  - cron: '0 0 * * *'
```

4. **Manual Triggers**:
   - Manual triggers allow pipeline execution through the GitLab web interface or API.
   - Provides flexibility for users to execute pipelines on-demand when needed.
   - Useful for tasks that require manual intervention or initiation, such as deployments or releases.

Certainly! Here's an additional section about stages in GitLab CI/CD:

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

