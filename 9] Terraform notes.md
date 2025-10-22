# **Terraform: A Comprehensive Guide**


> 	NOTE: while it is encouraged to memorize as much as possible for all the notes in this pack, with terraform, because of how individualized each provider is, it is not possible to memorize all the configurations for everything. You will need to memorize how projects are structured and how to leverage logic and tricks to make powerful, reusable, and extensible terraform projects. For provider, module, and resource specific configuration options, look up the official docs and reference them.

---
# **What it is/why we use it:**
Terraform is a powerful infrastructure as code (IaC) tool that enables users to define and provision infrastructure resources across various cloud providers. It simplifies the process of managing infrastructure by allowing you to express your desired state in a declarative manner. With Terraform, you can easily create, modify, and destroy infrastructure resources by writing code, providing a consistent and reproducible way to manage your infrastructure. 

One of the best use cases for Terraform is infrastructure provisioning and management in a cloud/multi-cloud environment. Whether you are working with AWS, Azure, GCP, or other cloud providers, Terraform allows you to define your infrastructure requirements in code, making it easy to spin up and tear down resources as needed. This makes it ideal for automating the deployment of complex architectures, including virtual machines, load balancers, databases, and networks. Another key use case for Terraform is that Terraform can be used to manage local VMs like Hyper-V and vSphere. 

Terraform provides plugins called "providers" that allow you to interact with different infrastructure platforms and services, including local infrastructure. For example, the Terraform vSphere provider allows you to manage vSphere resources, similarly, the Terraform AWS provider allows you to manage EC2 instances, s3 buckets, and other resources. 


---

# **Installing Terraform**
To get started with Terraform, you need to install it on your local machine. Here's how (this covers downloading from .zip, there are also package based installation instructions on the terraform website)
#### 1. Download Terraform:
Visit the official Terraform website (https://www.terraform.io/downloads.html) and download the appropriate binary for your operating system (e.g., Windows, macOS, Linux).
#### 2. Install Terraform:
##### For Windows:
1. Download the zip and then extract the downloaded ZIP archive to a directory of your choice.
2. Add the directory containing the Terraform binary to your system's PATH environment variable.
##### For Linux:
1. Download the ZIP for the most recent version, you can use this command just replace the version with the most recent ver: 
``` shell
curl https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip -o /tmp/terraform.zip
```
2. Unzip the file and then move the Terraform binary to a directory included in your PATH. You can do this by running a command like `cd /tmp && unzip terraform.zip && sudo mv terraform /usr/local/bin/` on Linux.
#### 3. Verify the Installation:
Open your terminal or command prompt and run:

```shell
terraform --version
```

You should see the installed Terraform version displayed. This confirms that Terraform is installed and ready to use.



---
# **Core terraform terminology**

1. **HCL:** stands for Hashicorp Configuration Language, the language Terraform uses for its projects.
   
2. **TF:** abbreviation for Terraform

3. **State file:** This file is created by TF and keeps track of the current state of your infrastructure. This file helps Terraform/you understand what resources have been created + changes that have occurred/need to occur.

4. **Plan:** The `Terraform Plan` command/action generates a plan + allows you to preview infrastructure changes that will be applied. On the backend, once you run this command, TF will analyze configuration specifications and compare them with the current state, it will then generate output that details actions that will be taken if you apply the changes.

5. **Apply:** The `terraform apply` command/action applies changes specified in the created plan. It will create/update/delete resources depending on your configurations.
   
6. **outputs:** These are values created by TF after infrastructure has been created /updated. They display information about your infrastructure.
   
7. **Workspaces:** TF workspaces are used to manage multiple environments (Dev, testing, staging, production) via separate configs/state files. These are essential for keeping your environment infrastructure files organized.

8. **Remote backend storage:** Some form of this term is often used to describe a state file storage location that is not local, can be object storage server, cloud storage, HashiCorp TF cloud, etc.

9.  **Terraform Cloud:** Terraform Cloud is often used as a control plane and state backend in enterprise environments. It is a SaaS platform provided by HashiCorp (now IBM) that integrates tightly with Terraform CLI, Git repositories, and CI/CD pipelines to enable automated, collaborative infrastructure management. Terraform Cloud manages **remote state**, versioning, and locking, preventing concurrent changes that could lead to state corruption. It also provides **workspaces**, which map a set of Terraform configuration files to an isolated state and variable set, enabling multiple environments (dev, staging, prod) to coexist using the same code. Additional features include **policy enforcement** via Sentinel, integrated **VCS triggers** for automated plan/apply runs on pull requests, and **variable management** for sensitive credentials or environment-specific settings. In enterprise workflows, Terraform Cloud acts as the central authority for state and execution, supporting both manual and automated Terraform runs while maintaining auditability and governance. [source of truth](https://medium.com/@devopsdiariesinfo/terraform-cloud-and-workspaces-a22a07e55446)



---

# Terraform project core components
Terraform can be a tad bit confusing without understanding the core project components in depth, so we will cover that here, from highest level to lowest level.

### (1) Providers
The provider is what we use to interact with a target platform, for example GCP, AWS, or Azure.  
This is a top-level specification, but because Terraform projects can be broken down into many files, it won’t always be specified at the top of `main.tf`. It’s common to see it placed in a separate file called `providers.tf`.

You can define multiple providers if you intend to interact with multiple platforms. For example:
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    sumologic = {
      source  = "sumologic/sumologic"
      version = "~> 2.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
  default_tags {
    tags = {
      Environment = "test"
      Service     = "some_service"
    }
  }
}
```
You’ll notice that the AWS provider has some additional configuration. This is standard. Each provider has its own set of configuration options, and you’ll need to reference the [Terraform provider registry](https://registry.terraform.io/browse/providers) when defining or customizing one.  
Also note that the `terraform {}` block defines general project-level settings (like required providers or backend config), while the `provider {}` block specifies how to authenticate and interact with that specific service.

### (2) Modules
Modules are where things can start to get a bit confusing. A module is a bundle of resources (we’ll cover what a resource is in the next section). Modules can be created by you or by others. Pre-built modules can be found in the [Terraform Registry](https://registry.terraform.io/browse/modules?product_intent=terraform).

MODULES ARE NOT PROVIDER LOCKED, while modules usually reference only one provider a module can use multiple providers, e.g. AWS & SumoLogic & Azure, if the use-case called for it

The point of modules is reusability. For example, if we have a VPC called `VPC-1` composed of several resources, we can create a module for it. That module can then be reused to spin up identical VPC environments by simply passing in different variables such as environment names, instance types, or images.

Modules don’t have to be a single file—many Terraform project structures break up modules by component.

Modules are called either by referencing a registry or git source, or by referencing a local path within your codebase. For example:
```
module "vpc" { # Implements the AWS-provided VPC module from the Terraform Registry
  source = "terraform-aws-modules/vpc/aws"
  name   = "my-vpc"
  cidr   = "10.0.0.0/16"
}

module "vpc1" { # Implements our custom VPC1 module from a local path
  source       = "../modules/vpc1"
  name         = "my-vpc-1"
  cidr         = "10.0.0.0/16"
  ec2_type     = "t2.micro"
  random_env   = "value"
}
```
Our custom module directory might look something like this:
```
/modules/vpc1
           |
           |- vpc.tf      # sets up networking
           |- iam.tf      # sets up permissions
           |- compute.tf  # sets up EC2 instances
           |- variables.tf
           |- outputs.tf
           |- providers.tf

# at its most minimal state however a main.tf could include all the resources configured by vpc, iam, and compute .tf as well as the outputs and providers, so it could just look like:
---|
   |-main.tf
   |-variables.tf
```
Modules often include `variables.tf` (to define input parameters) and `outputs.tf` (to expose values for use elsewhere). NOTE that when you implement a specific module you should look at its documentation to determine its configurability options, they will all differ.

### (3) Resources
A resource is the most minimal building block Terraform can create. It represents a single object provisioned via API interaction with the provider. Examples include an IAM policy, a VM instance, or a container.
EVERY RESOURCE IS LOCKED TO A PARTICULAR PROVIDER. A bundled collection of resources forms a module, which as stated above, isn't provider locked.

 For example, the resource below creates a single EC2 instance:
```
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  region        = "us-west-1"
}
```
Resources are declarative—Terraform ensures that the actual infrastructure matches the desired state you’ve defined here. Reference the docs for the particular resource you are implementing to determine its configurability options.



---


# Terraform project patterns in enterprise environments

Terraform is implemented with a GitOps-style approach in nearly all enterprise environments. Using Terraform without version control is nearly impossible due to the way most CI/CD pipelines or Terraform Cloud (commonly used to orchestrate and control Terraform-managed infrastructure) ingest and execute Terraform code, especially in response to repository changes. Enterprise Terraform design revolves around separation of environments, control of state, and strict change governance. Below we’ll break down common Terraform hierarchy patterns used in production environments.

### Pattern 1 - repo-per-service-main-per-environment

This hierarchy is probably the most common in enterprise environments where we have separation of environments (Dev, pre-prod, prod, etc.). Note that there isn’t a single “official” name mandated for this pattern by Terraform itself, but this pattern is extremely common in enterprise Terraform usage. 
Under this model, we have a Terraform repo per service, with each environment (e.g., dev, stage, prod) having its own main Terraform entry point (main.tf) that references shared modules but maintains isolated state files and variable sets. This allows changes to be tested and promoted through environments independently, with full control over drift and approval flow. 
Example structure:
```
/terraform
   |- environments/
   |      |- dev/
   |      |    |- main.tf
   |      |    |- variables.tf
   |      |    |- terraform.tfvars
   |      |
   |      |- stage/
   |      |    |- main.tf
   |      |    |- variables.tf
   |      |    |- terraform.tfvars
   |      |
   |      |- prod/
   |           |- main.tf
   |           |- variables.tf
   |           |- terraform.tfvars
   |
   |- modules/
          |- vpc/
          |- compute/
          |- iam/
          |- logging/
```
Each environment’s main file calls the same modules but is isolated via environment-specific variable files and separate backend configurations (e.g., S3 buckets or Terraform Cloud workspaces). 
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform workspace select dev
terraform init
terraform plan -var-file=./environments/dev/apollo_terraform.tfvars
terraform apply -var-file=./environments/dev/apollo_terraform.tfvars
```


### Pattern 2 - workspace-per-environment
A simpler variant of pattern 1 where the root module has a single main.tf, variables.tf, outputs.tf, and modules are shared, while each environment only maintains its own tfvars file (no separate main.tf per environment). State and variable sets are isolated via workspaces (dev, staging, prod) and the environment specific vars file. 
NOTE about workspaces, Each workspace maintains its **own separate state file**, even if you are using the **same set of Terraform configuration files**. This is a built-in mechanism to isolate environments (or multiple deployments) without duplicating code.
- Both **local workspaces** and **Terraform Cloud workspaces** allow you to use the **same Terraform configuration files** to manage **multiple isolated state files**.

- Each workspace maintains its own state so that changes applied in one workspace do not affect others.
You can switch between workspaces with `terraform workspace select <name>` locally or in Terraform Cloud, and Terraform will operate against the corresponding state.

Example structure:
```
/terraform
   |- main.tf
   |- variables.tf
   |- outputs.tf
   |- modules/
   |      |- vpc/
   |      |- compute/
   |      |- iam/
   |
   |- envs/
          |- dev.tfvars
          |- test.tfvars
          |- prod.tfvars
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform workspace select staging
terraform init
terraform plan -var-file=./envs/staging.tfvars
terraform apply -var-file=./envs/staging.tfvars
```

### Pattern 3 - repo-per-environment
Each environment lives in its own repository, often with separate pipelines, backends, and access permissions. 
Example structure:
```
repo: terraform-dev
repo: terraform-stage
repo: terraform-prod
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
terraform init
terraform plan -var-file=./apollo_terraform.tfvars
terraform apply -var-file=./apollo_terraform.tfvars
```

### Pattern 4 - component-based (monorepo with stacks)
Infrastructure broken into components (networking, security, compute, observability). Each component has its own stack, lifecycle, and environment separation inside the component. 
Example structure:
```
/terraform
   |- networking/
   |- security/
   |- compute/
   |- observability/
```
**Terraform Cloud / CI/CD / invocation snippet:**
```bash
cd networking/dev
terraform init
terraform plan -var-file=../../envs/dev.tfvars
terraform apply -var-file=../../envs/dev.tfvars
cd ../../compute/dev
terraform init
terraform plan -var-file=../../envs/dev.tfvars
terraform apply -var-file=../../envs/dev.tfvars
```

## Comparison table

| PATTERN NAME                                 | Structure / Key Idea                                                                                                | Terraform Cloud / CI/CD Integration                                                | Advantages                                                                     | Disadvantages                                                        |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **Repo-per-service, main-per-environment**   | Separate repo per service, each env has its own main.tf, variables.tf, tfvars                                       | Workspace per environment optional; init, plan, apply with env-specific tfvars     | Strong environment isolation, independent pipelines, parallel execution        | Folder duplication, cross-env orchestration needed                   |
| **Only tfvars per environment (Pattern 1b)** | Single root module, shared main.tf, variables.tf, modules; envs differ only by tfvars                               | Workspace per environment optional; plan/apply with env-specific tfvars            | Minimal code duplication, easy onboarding of new environments                  | Less explicit state separation, requires careful varfile discipline  |
| **Workspace-per-environment**                | Single codebase, multiple Terraform workspaces (dev, staging, prod)                                                 | Workspace selection controls environment; single repo                              | Minimal duplication, easy to add environments                                  | Harder isolation, accidental apply possible                          |
| **Repo-per-environment**                     | Each environment in a separate repository                                                                           | Each repo linked to its own workspace in Terraform Cloud; independent pipelines    | Maximum isolation, independent approvals, ideal for regulated environments     | High maintenance overhead, requires strict module version management |
| **Component-based (monorepo with stacks)**   | Infrastructure split into components (networking, security, compute, observability) with per-component environments | Component stacks applied in order; use outputs / remote state to link dependencies | Scales well, clear separation of responsibility, parallel development possible | Complex orchestration, careful dependency management required        |


---
---
> IMPORTANT 1 - Terraform projects — when we say _project_, think of an environment folder from Hierarchy 1 above — always contain a `main.tf`.  
> Modules, however, may either have their components split into multiple files (for example `vpc.tf`, `ecs.tf`, `iam.tf`) or combined into a single `main.tf` that defines everything — but not both.
---
---

# Terraform File Types
> 	See the second to last section on this notes page for some common file snippet examples.

- **main.tf** – The primary configuration file where you define infrastructure resources and their relationships. This is the core of any Terraform project and the only file strictly required for Terraform to run. All other files exist to support or extend what’s defined here.

- **variables.tf** – Terraform is Declarative as we have established, so this file **Declares** input variables used throughout your configuration. Variables make code reusable and environment-agnostic by allowing you to pass values (for example region, environment name, or instance type) without modifying source code directly.
    Variables, unless a default value is set OR provided via CLI, are passed through:
    - **terraform.tfvars** – Provides values for the variables declared in `variables.tf`. Typically holds environment-specific or sensitive data. This file is automatically loaded if present and allows for clean separation between configuration logic and data.
    - **.auto.tfvars–** Works the same way but supports multiple files. Terraform automatically loads all files in the working directory ending with `.auto.tfvars` in alphabetical order. This allows separate varfiles per environment (for example `dev.auto.tfvars`, `prod.auto.tfvars`) without needing `-var-file` flags. The key difference: `terraform.tfvars` is a single default file, while `.auto.tfvars` is a flexible pattern-based system that supports multiple autoloaded configurations.

- **locals.tf** – Defines local variables, which are computed values derived from expressions or inputs. Locals reduce duplication and make complex configurations easier to read by centralizing computed logic.

- **data.tf** – Declares data sources that retrieve existing infrastructure or external information (for example, fetching the latest AMI ID or an existing subnet). Data blocks are read-only and do not create resources, but they’re often referenced by `main.tf` resources.

- **outputs.tf** – Defines values Terraform should display or pass to other systems after deployment. Outputs often expose resource IDs, IP addresses, or ARNs that other modules or pipelines depend on.

- **providers.tf** – Declares and configures providers if they’re not already defined in `main.tf`. Providers tell Terraform how to communicate with external platforms (AWS, Azure, GCP, etc.). Many teams keep provider settings here for clarity and to simplify upgrades.

- **backend.tf** – Defines the remote backend used to store Terraform state files (for example, S3, Terraform Cloud, or Azure Storage). This ensures centralized, consistent state management across users and pipelines.

- **versions.tf** – Specifies the required Terraform version and provider versions to maintain consistency across teams and environments. Typically includes a `terraform` block with `required_version` and `required_providers`.

- **terraform.tfstate** – Tracks the current state of your deployed infrastructure. Terraform uses this file to determine what exists and what must be created, updated, or destroyed. In enterprise environments, this file should always reside in a remote backend defined in `backend.tf`. See section below for a little more information on this file.

- **terraform.tfstate.backup** – A local backup automatically generated when the main state file changes. It provides a rollback point if the active state becomes corrupted or misapplied.

- **.terraform.lock.hcl** – A dependency lock file automatically generated by `terraform init`. It pins provider versions to ensure consistent behavior across runs and environments, preventing unexpected provider upgrades.

- **.terraformignore** – Functions like `.gitignore` for Terraform Cloud and Enterprise. It specifies which local files or directories should be excluded from upload (for example `.git/`, `*.tfstate`, `.terraform/`).

- **Terraform.rc file:** - A Terraform RC file, or  `terraform.rc`  file, helps you define persistent CLI settings, such as environment variables and CLI configuration flags, which are automatically loaded when you run Terraform commands. The  `terraform.rc`  file is typically placed in the user's home directory (e.g.,  `~/.terraformrc`  on Unix-based systems or  `%APPDATA%\terraform.rc`  on Windows). It uses the HCL (HashiCorp Configuration Language) syntax to define the configuration options. With this you can set various configurations, such as default provider configurations, backend configurations, and environment variables. It allows you to customize and streamline your Terraform workflow by avoiding repetitive command-line options or environment variable settings. [Source of truth](https://developer.hashicorp.com/terraform/cli/config/config-file)


**Directories**

- **modules/** – Organizes reusable Terraform modules. Each subdirectory defines a discrete piece of infrastructure such as networking, compute, or IAM. This structure promotes reusability and maintainability across multiple projects or environments. See Note above this section for an additional point on the files in this directory.

- **.terraform/** – Created automatically by `terraform init`. Contains cached provider plugins, module source code, and workspace metadata. It should never be modified manually or committed to version control.



---

## **Terraform State File – Why It Matters**
The `.tfstate` file is **Terraform’s memory** of what’s been deployed.

**Uses:**
- Tracks **real-world infrastructure** so Terraform can detect changes.
- Allows **efficient updates** (e.g., change only one property instead of replacing an entire resource).
- Enables **accurate dependency resolution** between resources.

Without the state file, Terraform wouldn’t know what exists or what to change.


---

# **Terraform Commands**

These are the Terraform commands you’ll use most frequently — ordered by importance and real-world usage frequency in enterprise environments.

1. **terraform init**  
    Initializes a new or existing Terraform project by downloading required provider plugins, modules, and setting up the working directory. This must be run once in each project folder (see important 1 above) before any other command after cloning or modifying a configuration.

2. **terraform plan**  
    Generates and shows the execution plan — a preview of what Terraform will create, update, or destroy. It does **not** make changes.  minimally this command is just `terraform plan` 
	but best practice is usually (when planning manually):
```bash
terraform plan -out=generated.tfplan -no-color | tee readable.tfplan
```
This saves a binary plan file (`.tfplan`) for safe apply and creates a colorless readable version for review. Always review before applying to prevent infrastructure drift.

3. **terraform apply**  
    Applies the execution plan to create or modify infrastructure. It prompts for confirmation unless `-auto-approve` is used.  When run as just `terraform apply` it will generate a plan, ask for approval, and then apply the configuration, but if you planned separately and saved the plan as a file you can apply it with:
```bash
terraform apply ./plans/generated.tfplan

# you can also overwrite / designate variables on apply with the var flag specifying a standalone variable or a vars file.
terraform apply -var="somevar=value"
terraform apply -var-file=./envs/dev.tfvars ./plans/generated.tfplan
```
Applying a saved plan ensures consistency between planning and applying phases — critical in automated pipelines. Most enterprise environments use a remote state backend (terraform cloud/s3/etc.) to aggregate and save their state files.

4. **terraform destroy**  
    Tears down all infrastructure created by the configuration. It also prompts for confirmation unless `-auto-approve` is used.  
    Used cautiously — usually in dev/test environments or teardown pipelines.

5. **terraform validate**  
    Validates syntax and internal consistency of your Terraform configuration. This checks for structural errors before running `plan`. Often automated as a pre-commit or pipeline step.

6. **terraform fmt**  
    Automatically formats Terraform code into the canonical style for readability and consistency. Run regularly or enforce it through CI to maintain clean diffs and consistent reviews.

7. **terraform show**  
    Displays details of a Terraform state file or plan file. Commonly used to inspect deployed infrastructure or review plans.
```bash
terraform show terraform.tfstate
terraform show generated.tfplan
```

8. **terraform output**  
    Retrieves the output values defined in `outputs.tf`. Often used to extract resource identifiers, IPs, or ARNs for use in pipelines or scripts.
```bash
terraform output vpc_id
terraform output -json
```

9. **terraform state**  
    Manages and inspects Terraform’s state file directly. Common subcommands include `list`, `show`, `mv`, and `rm`:
```bash
terraform state list
terraform state show aws_instance.my_instance
terraform state mv aws_instance.old aws_instance.new
```
Used cautiously — manual edits can desync real infrastructure from Terraform’s view.

10. **terraform import**  
    Brings an existing real-world resource under Terraform management without recreating it.
```bash
terraform import aws_instance.my_instance i-1234567890abcdef0
```
Critical when onboarding legacy infrastructure or transitioning to Infrastructure as Code.

11. **terraform refresh**  
    Updates the local or remote state file to reflect the current status of real infrastructure **without** modifying anything. Helps detect drift between declared and actual resources.

12. **terraform apply -target=**  
    Applies changes to a specific resource or module only.
```bash
terraform apply -target=aws_instance.my_instance
```
Useful for partial updates or dependency debugging, but not recommended as a routine workflow — it can cause long-term drift if used excessively.

13. **terraform taint**  
    Marks a resource for destruction and recreation during the next apply.
```bash
terraform taint aws_instance.my_instance
```
Used when a resource is unhealthy or misconfigured but still exists in state.

14. **terraform untaint**  
    Reverses a taint, preventing replacement on the next apply.
```bash
terraform untaint aws_instance.my_instance
```

15. **terraform workspace**  
    Manages multiple workspaces — isolated state files within the same configuration (for example dev, stage, prod). Each workspace maintains its **own separate state file**, even if you are using the **same set of Terraform configuration files**. This is a built-in mechanism to isolate environments (or multiple deployments) without duplicating code.
```bash
terraform workspace new dev
terraform workspace select dev
terraform workspace list
```
Useful for lightweight environment separation when not using fully distinct backend directories.

16. **terraform graph**  
    Generates a dependency graph in DOT format, which can be rendered using tools like Graphviz to visualize relationships between resources and modules.

17. **terraform console**  
    Opens an interactive console for evaluating Terraform expressions, testing interpolations, and inspecting state data. Helpful for debugging variable or expression logic.


---

# **Terraform variables and data ingestion**

### Variable and data types
In Terraform, there are three primary ways to define or ingest data into your configurations: variables, locals, and data sources. Each serves a distinct purpose in managing input, derived, or external information.

1. **Data Sources** — Data sources let Terraform query and reference existing infrastructure or external data. They don’t create resources but allow you to _read_ and use information from outside the current configuration. **Data sources can return either a single value or a list/map of values**, depending entirely on the specific provider and data source schema. For example, you can look up an existing network switch or fetch the latest container image from a registry:
```hcl
data "aws_ec2_host" "test" {
  filter {
    name   = "instance-type"
    values = ["c5.18xlarge"]
  }
}
```
Data sources are often used to make your configuration dynamic and environment-aware, avoiding hardcoding of values that already exist in your infrastructure. Data sources return a structured object — essentially a map of key/value attributes — the attributes per data source differ, so we have to look in the provider specific documentation [example](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ec2_host); these attributes are the collected information we can parse out.


2. **Locals** — Locals are used to define customized values or to simplify repetitive expressions. They act like temporary variables within your Terraform configuration, computed at runtime. For instance, you might combine data from a data source with static text:
    ```hcl
    locals {
      sample_local = "${data.hyperv_network_switch.default.name}-suffix"
    }
    ```
    Locals help keep code DRY (Don’t Repeat Yourself) and improve readability, especially when working with complex expressions or repeated values across modules.


3. **Input Variables** — Input variables define configurable parameters for your Terraform project. They are typically declared in a separate file (commonly `variables.tf`) and can be set via `.tfvars` files, environment variables, or CLI flags. Variables make your code flexible and environment-agnostic. Example:
    ```hcl
    variable "region" {
      type    = string
      default = "us-east-1"
      description = "AWS region where resources will be deployed"
    }
    ```
    Variables are best used to customize deployments across environments (for example, `dev`, `stage`, `prod`) without modifying your core Terraform logic.
    
    
### Calling variable and data types
There are a few key points to understand when referencing variables and data in Terraform:

1.**Variables** are referenced with the `var.` prefix — for example, `var.region` or `var.instance_type`.

UNLESS

2. We are modifying the value, best practices then indicate we use double quotes and nest the variable in `${}` e.g. `"${var.variableName}.specialValue-${var.region}`

3. **Data sources** are always referenced using the format `data.<provider_type>.<name>.<attribute>`.  
    Example:
    ```hcl
    data "aws_ec2_host" "test" {
      host_id = "i-abc123"
    }
    
    output "ec2_sockets" {
      value = data.aws_ec2_host.test.sockets
    }
    ```
    Here, Terraform queries the specified EC2 host and exposes attributes (like `sockets`, `availability_zone`, `host_recovery`, etc.) that can be referenced anywhere else in your configuration. In the above example we output that value.
    

---


# **Terraform Logic / Conditionals / Operators**

Terraform includes built-in logic expressions, conditionals, and operators that make configurations dynamic and reusable across environments. They allow you to control resource creation, parameter values, and behavior based on input variables, environment types, or external data without duplicating code.

### **1. Conditional Expressions (Ternary Operator)**
Terraform supports a ternary-style conditional operator of the form `condition ? true_value : false_value`. It returns `true_value` if the condition is true, otherwise `false_value`. The ternary operator can be used in many places: with `count` to conditionally create resources, to dynamically assign argument values, or to set locals based on conditions.

Example with resource arguments:
```hcl
variable "environment" { type = string }
resource "aws_instance" "example" {
  instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"
  monitoring    = var.environment != "dev" ? true : false
}
```
Example in locals for dynamic values:
```hcl
locals {
  subnet_cidr   = var.env == "prod" ? "10.0.0.0/16" : "10.1.0.0/16"
  enable_backup = var.env == "stage" ? false : true
}
```
Example with `count` for conditional resource creation:
```hcl
resource "aws_s3_bucket" "logs" {
  count  = var.enable_logging ? 1 : 0
  bucket = "app-logs-${var.env}"
}
```
Example inside a map:
```hcl
locals {
  tags = {
    Environment = var.env
    Owner       = var.env == "prod" ? "team-prod" : "team-dev"
  }
}
```

### **2. Boolean Logic Operators**
Terraform provides standard logical operators: `&&` for AND, `||` for OR, and `!` for NOT. They can be combined with variables, comparisons, or ternary expressions to control arguments or conditional resource creation.

Example with AND:
```hcl
resource "aws_instance" "example" {
  count = var.env == "prod" && var.enable_monitoring ? 1 : 0
}
```
Example with OR:
```hcl
locals {
  enable_feature = var.env == "dev" || var.env == "stage"
}
```
Example with NOT:
```hcl
locals {
  skip_vpc_creation = !var.create_vpc
}
```

### **3. Comparison Operators**

Terraform supports basic comparison operators: `==`, `!=`, `>`, `<`, `>=`, `<=`. These are often combined with ternary or boolean logic to evaluate conditions dynamically.

Examples:
```hcl
locals {
  use_large_instance = var.instance_count > 3
  is_primary_region  = var.region == "us-east-1"
  needs_backup       = length(var.subnets) >= 3
  skip_cache         = var.enable_cache != true
}
```

### **4. Conditional and Iterative Resource Creation**
`count` and `for_each` provide flexible ways to create resources based on conditions or iterate over multiple items.

**`count` Meta-Argument:** `count` determines how many instances Terraform creates. Using a ternary operator, `count` can conditionally enable or disable a resource:
```hcl
resource "aws_iam_role" "lambda_exec" {
  count = var.converged_env ? 1 : 0
  name  = "lambda-role-${var.env}"
}
```
`count = 0` skips the resource, `count = 1` or higher creates it.

**`for_each` Meta-Argument:** `for_each` iterates over sets or maps to create uniquely identified instances. Unlike `count`, it uses keys for direct referencing:
```hcl
variable "app_names" { type = list(string) default = ["api","frontend","worker"] }
resource "aws_s3_bucket" "buckets" {
  for_each = toset(var.app_names)
  bucket   = "myapp-${each.key}"
}
```
Example using a map for more complex attributes:
```hcl
variable "env_configs" {
  type = map(object({ cidr : string }))
  default = {
    dev  = { cidr = "10.0.1.0/24" }
    prod = { cidr = "10.0.2.0/24" }
  }
}
resource "aws_subnet" "example" {
  for_each = var.env_configs
  vpc_id   = aws_vpc.main.id
  cidr_block = each.value.cidr
}
```

### **5. Built-in Functions for Logic**
Terraform functions can evaluate or manipulate data dynamically. Examples:
```hcl
locals {
  region = coalesce(var.region, "us-east-1")
  is_enabled = contains(var.enabled_envs, var.env)
  subnet_count = length(var.subnets)
  owner = lookup(var.tags, "Owner", "default-owner")
  vpc_id = try(data.aws_vpc.main.id, null)
}
```

### **6. Locals for Consolidating Logic**
Locals define temporary, computed values to simplify expressions and reduce repetition. Example:
```hcl
locals {
  instance_type = var.env == "prod" ? "t3.large" : "t3.micro"
  enable_backup = contains(["prod","stage"], var.env)
  ami_id = var.env == "prod" ? data.aws_ami.prod.id : data.aws_ami.dev.id
}
```
Reference them with `local.instance_type`, `local.enable_backup`, or `local.ami_id`.

### **7. Dynamic Blocks and For Expressions**
Dynamic blocks programmatically generate nested blocks:
```hcl
resource "aws_security_group" "example" {
  name = "dynamic-example"
  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from
      to_port     = ingress.value.to
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}
```
For expressions filter or transform lists/maps:
```hcl
locals {
  prod_subnets = [for s in var.subnets : s if s.env == "prod"]
  names_map    = { for n in var.names : n => upper(n) if n != "" }
}
```

### **8. Summary Table**

|Feature|Purpose|Example|
|---|---|---|
|Ternary Operator|Inline branching logic|`var.env == "prod" ? "t3.large" : "t3.micro"`|
|Boolean Operators|AND/OR/NOT conditions|`var.enabled && var.region == "us-east-1"`|
|Comparison Operators|Evaluate values|`length(var.subnets) > 2`|
|`count`|Conditional resource creation|`count = var.enable ? 1 : 0`|
|`for_each`|Iterate resources|`for_each = toset(var.items)` or `for_each = var.map`|
|Functions|Dynamic evaluation|`lookup(var.map, "key", "default")`|
|Locals|Compute reusable values|`local.instance_type`|
|Dynamic Blocks|Programmatically generate nested blocks|`dynamic "ingress" { for_each = ... }`|
|For Expressions|Build filtered lists/maps|`[for x in var.list : x if x != ""]`|



---
---
> The above section is quite complex, for some quick and easy tips and tricks to implement in your playbooks check out the below section
---
---


# **Essential tips and tricks**

 1. **Use `count` with a ternary operator and a variable to control resource creation**
 When working with reusable Terraform modules—especially under patterns like **repo-per-service-main-per-environment**—it’s common to introduce new resources or components that not all environments should create. For example, if you have a **converged environment** (two loosely isolated environments sharing some global resources such as IAM roles, S3 buckets, or networking), you don’t want Terraform to attempt creating duplicate global resources.  
In these cases, you can control whether a resource is created using the `count` meta-argument combined with a conditional (ternary) expression based on a flag variable. Example:
```hcl
# variables.tf
variable "converged_env" {
  type    = bool
  default = false
}

# env.auto.tfvars
converged_env = true

# main.tf (or inside a module)
resource "aws_iam_role" "lambda_exec" {
  count = var.converged_env ? 0 : 1
  name  = "lambda-exec-role"
  assume_role_policy = data.aws_iam_policy_document.lambda_assume.json
}
```
In this example, when `converged_env` is set to **true**, the IAM role will **not be created** (`count = 0`), since it already exists in a shared context. When `converged_env` is **false**, Terraform creates one instance (`count = 1`).  
You can invert the logic depending on your use case—for instance, `count = var.converged_env ? 1 : 0` if you only want the role in converged environments. The key takeaway is that `count` allows conditional resource instantiation without duplicating code or maintaining separate configuration files for each environment.


2. **Use `title()` to format strings**  
`title()` is a built-in Terraform function that converts the first letter of **each word** in a string to uppercase and the remaining letters to lowercase. It’s useful for enforcing consistent naming across environments. For example, if you have environment names like `Prod` or `Staging`, `title(var.environment_name)` ensures that user input such as `PROD` or `staging` is normalized to `Prod` and `Staging`, which helps maintain consistent tagging, naming conventions, and comparisons.
```hcl
provider "aws" {
  region = var.aws_region
  default_tags {
	  tags = {
	    Environment = title(var.environment_name)
	    Service     = "OrigamiJWKS"
	}
  }
}
```


---
#FLESH_OUT 
# **Mock Terraform Project**
```
terraform-project/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── dev.auto.tfvars
│   │   └── outputs.tf
│   └── prod/
│       ├── main.tf
│       ├── variables.tf
│       ├── prod.auto.tfvars
│       └── outputs.tf
├── modules/
│   ├── ec2/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── security_group/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── providers.tf
├── backend.tf
├── versions.tf
└── README.md

# Provider, backend, and versions files live at the repo root because they enforce consistency across all environments and modules. Providers define how Terraform talks to the target platform, backends control where state is stored, and versions pin Terraform and provider versions. Modules shouldn’t include these—they’re reusable, provider-agnostic, and inherit settings from the root configuration.
```

# **Module Implementations**
**modules/security_group/main.tf**
```hcl
resource "aws_security_group" "ssh_access" {
  name        = var.sg_name
  description = var.sg_description
  vpc_id      = var.vpc_id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.allowed_ip]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = var.tags
}
```
**modules/security_group/variables.tf**
```hcl
variable "sg_name" { type = string }
variable "sg_description" { type = string }
variable "vpc_id" { type = string }
variable "allowed_ip" { type = string }
variable "tags" { type = map(string) default = {} }
```
**modules/security_group/outputs.tf**
```hcl
output "sg_id" {
  value = aws_security_group.ssh_access.id
}
```

**modules/ec2/main.tf**
```hcl
resource "aws_instance" "ec2" {
  count                  = var.instance_count
  ami                    = var.ami
  instance_type          = var.instance_type
  subnet_id              = var.subnet_id
  key_name               = var.key_name
  vpc_security_group_ids = var.sg_ids

  tags = merge(var.tags, { Name = format("%s-%d", var.name_prefix, count.index + 1) })
}
```
**modules/ec2/variables.tf**
```hcl
variable "instance_count" { type = number }
variable "ami" { type = string }
variable "instance_type" { type = string }
variable "subnet_id" { type = string }
variable "key_name" { type = string }
variable "sg_ids" { type = list(string) }
variable "tags" { type = map(string) default = {} }
variable "name_prefix" { type = string }
```
**modules/ec2/outputs.tf**
```hcl
output "public_ips" {
  value = [for i in aws_instance.ec2 : i.public_ip]
}
```

# **Environment Example**
**environments/dev/main.tf**
```hcl
provider "aws" { region = "us-east-1" }

module "ssh_sg" {
  source        = "../../modules/security_group"
  sg_name       = "dev-ssh-sg"
  sg_description = "Dev SSH access"
  vpc_id        = var.vpc_id
  allowed_ip    = var.my_public_ip
  tags          = { Environment = title(var.environment_name) }
}

module "ec2_instances" {
  source        = "../../modules/ec2"
  instance_count = var.instance_count
  ami           = var.ami
  instance_type = var.instance_type
  subnet_id     = var.subnet_id
  key_name      = var.key_name
  sg_ids        = [module.ssh_sg.sg_id]
  tags          = { Environment = title(var.environment_name) }
  name_prefix   = "dev-app"
}

output "ec2_public_ips" {
  value = module.ec2_instances.public_ips
}
```
**environments/dev/variables.tf**
```hcl
variable "environment_name" { type = string default = "dev" }
variable "instance_count" { type = number default = 2 }
variable "ami" { type = string default = "ami-084a7d336e816906b" }
variable "instance_type" { type = string default = "t2.micro" }
variable "subnet_id" { type = string default = "subnet-123456" }
variable "vpc_id" { type = string default = "vpc-123456" }
variable "key_name" { type = string default = "my-key" }
variable "my_public_ip" { type = string default = "10.0.0.1/32" }
```
**environments/dev/dev.auto.tfvars**
```hcl
environment_name = "dev"
instance_count   = 3
```

# **Providers / Backend / Versions**

**providers.tf**
```hcl
provider "aws" { region = "us-east-1" }
```
**backend.tf**
```hcl
terraform {
  backend "s3" {
    bucket = "my-tf-state"
    key    = "dev/terraform.tfstate"
    region = "us-east-1"
  }
}
```
**versions.tf**
```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = { source = "hashicorp/aws" version = "~> 5.0" }
  }
}
```


# **Usage**
```bash
terraform init
terraform plan -out generated.tfplan
terraform apply ./generated.tfplan
```

This example shows how modules, `count`, outputs, variables, and providers work together in a small but realistic Terraform project. It is fully reusable for `prod` or other environments by creating another folder under `environments/` and modifying variables.



---
---
# Other information

---
---

# **Using Terraform in offline environments**
By default, Terraform downloads providers from the internet when you initialize a Terraform configuration. However, it is possible to work with Terraform in an airgapped or offline environment by manually installing providers locally.

To use providers without an internet connection, you can follow these steps:

1. Download Provider Binary: Obtain the provider binary file (.exe or other format) for the specific provider you need. You can download the provider binary from the provider's official website or other trusted sources.

2. Place Provider Binary: Copy the provider binary file to a local directory or a specific location within your Terraform project. You can create a "providers" directory within your project and place the provider binary there.

3. Configure Provider Locally: In your Terraform configuration file, specify the local path to the provider binary using the  `local-exec`  provisioner. For example: ```
```
provider "aws" {
  region = "us-west-2"
  executable = "path/to/local/provider/binary"
}
```

Replace  `"path/to/local/provider/binary"`  with the actual path to the provider binary file on your local system.

4. Initialize Terraform: Run the  `terraform init`  command in your project directory to initialize Terraform. Terraform will detect the locally configured provider and use it for subsequent operations.

By following these steps, you can work with Terraform and its providers in an airgapped environment without relying on internet connectivity. Ensure that you have the correct provider binary version compatible with your Terraform configuration and that it is obtained from a trusted source.



---
---
# emergency help section⚠️

---
---

# **I DELETED MY STATE FILE! / MY STATE IS LOST!**
If you accidentally **delete a Terraform state file**, your configuration no longer “knows” what resources exist. This is serious, but there are ways to reconcile your state depending on your setup. 
## 1. **Check for backups first**

- Terraform automatically creates a backup of your state: `terraform.tfstate.backup` (local) or your **remote backend** (S3, Terraform Cloud, etc.).
    
- **Restore the backup** if possible — this is usually the fastest and safest approach.
    
```bash
cp terraform.tfstate.backup terraform.tfstate
```
Or, in a remote backend (Terraform Cloud/S3), just reinitialize and pull the latest state:
```bash
terraform init
terraform refresh
```


## 2. **Re-import resources**

If no backup is available, you must **reconstruct the state** using `terraform import` for each existing resource.

1. Identify the resource in your cloud provider (e.g., an AWS EC2 instance).
    
2. Run `terraform import` to add it back to the state
```bash
terraform import aws_instance.web i-1234567890abcdef0
```

- Repeat this for all resources in the environment.
    
- The resource in your `.tf` files must **match the actual existing resource** for import to succeed.
    
## 3. **Rebuild state incrementally**

- You can also use `terraform refresh` after importing some resources to reconcile the live infrastructure with your `.tf` files.
    
- This updates the state file with real-world values without changing any resources:
```bash
terraform refresh
```

## 4. **Plan and verify**

Once the state is rebuilt:
```bash
terraform plan
```

- Terraform will show what it thinks needs to be created, modified, or destroyed.
    
- Carefully verify that Terraform isn’t trying to **recreate existing resources**, which would cause duplication or conflicts.
    
## 5. **Optional: Use remote backend snapshots**

- If using Terraform Cloud or an S3 backend with versioning, you may have **older snapshots** of your state file.
    
- Restore from the snapshot instead of manual re-importing.










---
add 

...- If you run `terraform init` and `terraform plan` in `environments/dev` **without `providers.tf` in dev**, Terraform uses the root provider (`us-east-1`).
    
- If `providers.tf` exists in `environments/dev`, Terraform **merges or overrides** the provider, so the module runs in `us-west-2`.
    
- Modules themselves never declare backends or providers—they inherit from whatever calls them.
    

This lets you centralize most config in the root while still supporting environment-specific provider tweaks.
**well-defined way of resolving provider and backend configurations**. When you run `terraform init` or other commands in a subdirectory (like `environments/dev`), Terraform looks **up the directory tree** for a `terraform` block with `required_providers` and backend configuration. That’s why keeping `versions.tf` and `backend.tf` at the repo root works—they’re automatically inherited by subfolders.

- **modules/** should generally **not** include providers or backends—they inherit them from whatever calls the module.
    
- **environments/** can have their own `providers.tf` if the environment needs provider overrides (like region or credentials), but it’s optional. If present, Terraform merges it with the root provider configuration.
    

So, if you have a provider at the root and no `providers.tf` in `environments/dev`, Terraform just uses the root one. If you put a `providers.tf` in the environment folder, it can override or supplement the root provider. It’s a flexible system, which is why you see both patterns in the wild.