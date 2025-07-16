


---
# Other (non ansible/terraform) Infrastructure as Code (IAC) Tools

1. **Salt (SaltStack)**
   - **Overview**: Salt is an open-source configuration management and orchestration tool, similar to Ansible, with a focus on speed and scalability. It uses a master-agent model but can also work in an agentless mode. Salt allows administrators to manage complex infrastructures using state files written in YAML or Python.
   - **Integration in DevOps**: Salt is commonly used to automate provisioning and configuration management across large, distributed environments. It integrates well with DevOps pipelines, enabling teams to manage infrastructure changes programmatically.
   - **Install**:
     ```bash
     sudo apt-get install salt-master salt-minion
     ```
   - **Example Usage**:
     A Salt state file (`install-nginx.sls`):
     ```yaml
     nginx:
       pkg.installed: []
       service.running:
         - enable: True
     ```
     Apply the state:
     ```bash
     salt '*' state.apply install-nginx
     ```
   - **DevOps Workflow Example**: 
     SaltStack can be integrated into CI/CD pipelines for automated deployment and configuration changes. For example, a Jenkins pipeline could trigger a Salt state to configure servers after deployment.




2. **Puppet**
   - **Overview**: Puppet is a popular configuration management tool that automates the provisioning, configuration, and management of infrastructure. It uses a declarative language to define desired states for infrastructure components. Puppet works on a master-agent model but can also operate in a masterless mode.
   - **Integration in DevOps**: Puppet is widely used in enterprise environments for managing large-scale infrastructures. It integrates with CI/CD tools like Jenkins and GitLab to ensure infrastructure consistency across environments.
   - **Install**:
     ```bash
     sudo apt install puppet-agent
     ```
   - **Example Usage**:
     A Puppet manifest (`nginx.pp`):
     ```puppet
     package { 'nginx':
       ensure => installed,
     }
     service { 'nginx':
       ensure => running,
       enable => true,
     }
     ```
     Apply the manifest:
     ```bash
     puppet apply nginx.pp
     ```
 



3. **Chef**
   - **Overview**: Chef is a powerful IAC tool that focuses on infrastructure automation. It uses "recipes" and "cookbooks" to define how infrastructure should be configured. Chef can manage both on-premise and cloud-based infrastructure and follows a master-agent architecture, though it can also run in a local mode (Chef Solo).
   - **Integration in DevOps**: Chef integrates into DevOps workflows by providing automated, continuous configuration management. Chef’s recipes can be versioned and managed in repositories like Git, making it easy to integrate with CI/CD pipelines.
   - **Install**:
     ```bash
     curl -L https://omnitruck.chef.io/install.sh | sudo bash
     ```
   - **Example Usage**:
     A basic Chef recipe (`default.rb`):
     ```ruby
     package 'nginx'
     service 'nginx' do
       action [:enable, :start]
     end
     ```
     Apply the recipe:
     ```bash
     chef-client --local-mode default.rb
     ```




3. **Vagrant**
   - **Overview**: Vagrant is a tool for building and managing virtualized development environments. It provides simple, repeatable setups using a `Vagrantfile`, which can define VMs or containers for development.
   - **Integration in DevOps**: Vagrant is primarily used in development and testing stages of a DevOps pipeline. It helps create consistent environments across development teams and integrates with tools like Docker, Ansible, and Puppet.
   - **Install**:
     ```bash
     sudo apt install vagrant
     ```
   - **Example Usage**:
     A basic `Vagrantfile`:
     ```ruby
     Vagrant.configure("2") do |config|
       config.vm.box = "ubuntu/bionic64"
       config.vm.provision "shell", inline: "apt-get update && apt-get install -y nginx"
     end
     ```
     Start the VM:
     ```bash
     vagrant up
     ```






---
# Build Tools

1. **Apache Ant**
   - **Overview**: Apache Ant is a Java-based build tool that allows developers to automate tasks such as compiling code, packaging binaries, and running tests. While it's flexible, it’s not as user-friendly as newer tools like Maven or Gradle. It can be used in DevOps workflows to automate the build process, particularly for legacy Java applications.
   - **Integration in DevOps**: In DevOps pipelines, Ant can be invoked during the continuous integration (CI) stage to compile code, execute unit tests, and package the application. It integrates with CI/CD tools like Jenkins and Bamboo for automated builds.
   - **Install**:
     ```bash
     sudo apt install ant
     ```
   - **Example Usage**:
     Create a `build.xml` file:
     ```xml
     <project name="MyProject" default="compile">
       <target name="compile">
         <javac srcdir="src" destdir="build/classes"/>
       </target>
     </project>
     ```
     Run Ant:
     ```bash
     ant compile
     ```
   - **DevOps Workflow Example**: 
     In Jenkins, you can define an Ant task in a pipeline like this:
     ```groovy
     pipeline {
       agent any
       stages {
         stage('Build') {
           steps {
             ant 'compile'
           }
         }
       }
     }
     ```




2. **Maven**
   - **Overview**: Maven is a widely-used build automation and project management tool primarily for Java. It uses a `pom.xml` file to manage dependencies, build lifecycle, and project structure.
   - **Integration in DevOps**: Maven is commonly used in DevOps pipelines for dependency management, automated builds, and integration testing. It integrates smoothly with CI/CD tools like Jenkins, GitLab CI, and Bamboo.
   - **Install**:
     ```bash
     sudo apt install maven
     ```
   - **Example Usage**:
     A basic `pom.xml` file:
     ```xml
     <project>
       <modelVersion>4.0.0</modelVersion>
       <groupId>com.example</groupId>
       <artifactId>my-app</artifactId>
       <version>1.0-SNAPSHOT</version>
     </project>
     ```
     Run Maven:
     ```bash
     mvn clean install
     ```
   - **DevOps Workflow Example**: 
     In a Jenkins pipeline, Maven can be executed as:
     ```groovy
     pipeline {
       agent any
       stages {
         stage('Build') {
           steps {
             sh 'mvn clean install'
           }
         }
       }
     }
     ```





---
# Continuous Delivery (CD) Tools

1. **Spinnaker**
   - **Overview**: Spinnaker is a multi-cloud continuous delivery platform that automates deployments across various cloud providers. It integrates well with Kubernetes, AWS, Google Cloud, and more.
   - **Integration in DevOps**: In a DevOps workflow, Spinnaker enables automated, policy-driven deployments and rollbacks across multiple environments. It integrates with CI systems like Jenkins and Bamboo and handles deployments to cloud platforms.
   - **Install**: 
     To install on Kubernetes:
     ```bash
     hal config deploy edit --type distributed --account-name your-k8s-account
     hal deploy apply
     ```
   - **Example Usage**:
     Once installed, you can define pipelines in the Spinnaker UI for automated deployments.
   - **DevOps Workflow Example**: 
     Spinnaker pipelines allow for canary releases and blue-green deployments. A typical pipeline might automatically trigger upon a successful Jenkins build and deploy to a staging environment.
links:
[OpsMx Tutorials - Learn Continuous Delivery with Spinnaker and Argo CD](https://www.opsmx.com/tutorials/)




2. **Argo CD**
   - **Overview**: Argo CD is a Kubernetes-native continuous delivery tool that syncs application definitions from Git repositories to Kubernetes clusters. It follows a GitOps model, ensuring the cluster state matches the desired state defined in Git.
   - **Integration in DevOps**: Argo CD is used in Kubernetes-based DevOps environments for continuous delivery. It automates application deployment by continuously monitoring Git repositories and applying updates to Kubernetes clusters.
   - **Install**:
     Install Argo CD in Kubernetes:
     ```bash
     kubectl create namespace argocd
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
     ```
   - **Example Usage**:
     Create an Argo CD application that tracks a Git repository:
     ```bash
     argocd app create my-app --repo https://github.com/example/app.git --path ./k8s --dest-server https://kubernetes.default.svc --dest-namespace default
     ```
   - **DevOps Workflow Example**: 
     Argo CD is often used in GitOps workflows where changes to the repository automatically trigger updates to Kubernetes environments.



3. **Bamboo**
   - **Overview**: Bamboo, developed by Atlassian, is a continuous integration and delivery tool that automates the release management process. It integrates seamlessly with other Atlassian tools like JIRA and Bitbucket.
   - **Integration in DevOps**: Bamboo automates build, test, and release processes, making it easier to manage complex pipelines. It integrates well with Docker, Kubernetes, and cloud services.
   - **Install**: 
     Download and install Bamboo from Atlassian's website:
     ```bash
     wget https://www.atlassian.com/software/bamboo/download
     ```
   - **Example Usage**: 
     A basic Bamboo plan might include steps like:
     - Clone repository from Bitbucket
     - Run unit tests
     - Build and deploy Docker containers
   - **DevOps Workflow Example**: 
     Bamboo plans are commonly used for parallel builds and deployments across multiple environments (dev, staging, production).




4. **CircleCI**
   - **Overview**: CircleCI is a CI/CD platform that supports cloud and self-hosted options. It’s known for its strong Docker integration, making it a popular choice for containerized application pipelines.
   - **Integration in DevOps**: CircleCI automates the testing and deployment process by allowing developers to define workflows in a `config.yml` file. It integrates well with Docker, Kubernetes, and cloud services.
   - **Example Usage**:
     A `config.yml` file:
     ```yaml
     version: 2.1
     jobs:
       build:
         docker:
           - image: circleci/python:3.8
         steps:
           - checkout
           - run: pip install -r requirements.txt
           - run: pytest
     workflows:
       version: 2
       build_and_test:
         jobs:
           - build
     ```




5.  **Travis CI**
   - **Overview**: Travis CI is a hosted continuous integration service that integrates with GitHub repositories to automate the build and testing process. It supports multiple programming languages and environments.
   - **Integration in DevOps**: Travis CI allows developers to automate builds and tests for every code push, helping catch issues early. It integrates with tools like Heroku, Docker, and cloud services for seamless deployments.
   - **Example

 Usage:
     A `.travis.yml` file:
   ```
language: python
python:
  - "3.8"
install:
  - pip install -r requirements.txt
script:
  - pytest
deploy:
  provider: heroku
  api_key: "$HEROKU_API_KEY"
  app: your-app-name

```
 
