# **Terraform: A Comprehensive Guide**


---

# **What it is/why we use it:**
Terraform is a powerful infrastructure as code (IaC) tool that enables users to define and provision infrastructure resources across various cloud providers. It simplifies the process of managing infrastructure by allowing you to express your desired state in a declarative manner. With Terraform, you can easily create, modify, and destroy infrastructure resources by writing code, providing a consistent and reproducible way to manage your infrastructure. 

One of the best use cases for Terraform is infrastructure provisioning and management in a cloud environment. Whether you are working with AWS, Azure, GCP, or other cloud providers, Terraform allows you to define your infrastructure requirements in code, making it easy to spin up and tear down resources as needed. This makes it ideal for automating the deployment of complex architectures, including virtual machines, load balancers, databases, and networks. 

Another key use case for Terraform is that Terraform can be used to manage local VMs like Hyper-V and vSphere. Terraform provides plugins called "providers" that allow you to interact with different infrastructure platforms and services, including local infrastructure. For example, the Terraform vSphere provider allows you to manage vSphere resources, such as VMs, datacenters, and networks. Similarly, the Terraform Hyper-V provider allows you to manage Hyper-V virtual machines and other resources. By using Terraform to manage your local VMs, you can achieve the same benefits as managing cloud infrastructure with Terraform, such as version control, collaboration, and reproducibility.

### Declarative Configuration
Terraform uses a declarative approach, where you define the desired state of your infrastructure in configuration files, rather than writing imperative scripts. This means you specify *what* you want, and Terraform figures out *how* to make it happen.

### Infrastructure as Code (IaC)
Terraform enables you to manage your infrastructure in a code-like manner. This allows for version control, collaboration, and reproducibility of your infrastructure. Infrastructure changes become code changes, making it easier to track and manage.

### Resource Graph
Terraform creates a dependency graph of the resources you define, ensuring that resources are created, updated, or destroyed in the correct order. This ensures consistency and avoids resource conflicts.

### State Management
Terraform maintains a state file that records the current state of your infrastructure. This state file is used to track changes and plan updates or modifications to your infrastructure. Proper state management is crucial for Terraform's operation.


---

# **Components of Terraform--**
### 1. Providers
Providers are responsible for managing and interacting with specific infrastructure or service providers such as AWS, Azure, Google Cloud, and more. Each provider has its own set of resources and data sources that you can use in your Terraform configurations.
### 2. Resources
Resources are the fundamental building blocks of your infrastructure. They represent the various resources you want to provision, such as virtual machines, networks, databases, and more. Resources are defined within provider blocks.
### 3. Variables
Variables are used to parameterize your Terraform configurations. They allow you to pass values into your configuration files, making it easier to reuse and customize your infrastructure definitions.
### 4. Outputs
Outputs allow you to extract and display specific values from your infrastructure after it's been provisioned. This is useful for retrieving information like IP addresses or DNS names of resources you've created.
### 5. Modules
Modules are reusable components of Terraform configurations. They enable you to encapsulate and abstract portions of your infrastructure code, making it easier to manage and share configurations across projects.
### 6. State
Terraform's state file records the current state of your infrastructure. It's essential for Terraform to track the state of resources, detect changes, and plan updates accurately. State can be stored locally or remotely, depending on your configuration.


---

# **Installing Terraform--**
To get started with Terraform, you need to install it on your local machine. Here's how (this covers downloading from .zip, there are also package based installation instructions on the terraform website)
#### 1. Download Terraform:
Visit the official Terraform website (https://www.terraform.io/downloads.html) and download the appropriate binary for your operating system (e.g., Windows, macOS, Linux).
#### 2. Install Terraform:
#### For Windows:
1. Download the zip and then extract the downloaded ZIP archive to a directory of your choice.
2. Add the directory containing the Terraform binary to your system's PATH environment variable.
#### For Linux:
1. Download the ZIP for the most recent version, you can use this command just replace the version with the most recent ver: 
``` shell
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
```
2. Unzip the file and then move the Terraform binary to a directory included in your PATH. You can do this by running a command like `unzip terraform_1.6.0_linux_amd64.zip && sudo mv terraform /usr/local/bin/` on Linux.
#### 3. Verify the Installation:
Open your terminal or command prompt and run:

```shell
terraform --version
```

You should see the installed Terraform version displayed. This confirms that Terraform is installed and ready to use.

---

# **Core terraform terminology**


1.  **Provider**: a provider in terraform is a plugin that defines/manages resources for a specific infrastructure platform (e.g. AWS, Azure, VMware, etc.). 

2. **Resource:** A resource is a infrastructure component that you specify to create/manage/change via Terraform (e.g. compute instances, databases, cloud networks, etc.).

3. **HCL:** stands for Hashicorp Configuration Language
 
4. **Module:** A module is a reusable/encapsulated unit of HCL code, we use modules to promote one of the core IAC pillars which is reusability/portability, we can reuse/port modules to enhance solution capabilities. Modules can be created by you or come from the Terraform Registry, which hosts community-contributed modules.

5. **outputs:** These are values created by TF after infrastructure has been created /updated. They display information about your infrastructure.

6. **State file:** This file is created by TF and keeps track of the current state of your infrastructure. This file helps Terraform/you understand what resources have been created + changes that have occurred/need to occur.

7. **Plan:** The `Terraform Plan` command generates a plan + allows you to preview infrastructure changes that will be applied. On the backend, once you run this command, TF will analyze configuration specifications and compare them with the current state, it will then generate output that details actions that will be taken if you apply the changes.

8. **Apply:** The `terraform apply` command applies changes specified in the created plan. It will create/update/delete resources depending on your configurations.

9. **Workspaces:** TF workspaces are used to manage multiple environments (Dev, testing, staging, production) via separate configs/state files. These are essential for keeping your environment infrastructure files organized.

10. **Remote backend storage:** Some form of this term is often used to describe a state file storage location that is not local, can be object storage server, cloud storage, HashiCorp TF cloud, etc.
   
   






---

# **Structuring Terraform Configuration Files For Your Project**
Organizing your Terraform configuration files effectively is essential for maintaining readability and manageability. Here's a common structure for a Terraform project (keep in mind  that terraform projects are directory based, each directory with a main.tf is a new terraform project, when you run the init terraform will look in the directory you are in for all the necessary files):

```
my-terraform-project/
├── main.tf               # Main configuration file
├── variables.tf          # Variable definitions
├── outputs.tf            # Output definitions
├── terraform.tfvars      # Variable values (optional)
├── terraform.tfstate     # Local state (usually .gitignore'd)
├── providers.tf          # Provider configurations
├── modules/              # Directory for reusable modules
│   ├── module1/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── module2/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
└── ...
```

- **main.tf**: The primary configuration file where you define your infrastructure resources and their relationships.

- **variables.tf**: This file defines input variables used in your configuration. Variables make your code more flexible and customizable.

- **outputs.tf**: Here, you define output values that you want to extract from your infrastructure after provisioning.

- **terraform.tfvars**: An optional file for setting variable values. It's useful for keeping sensitive information separate from your code.

- **terraform.tfstate**: Terraform's local state file. It keeps track of the current state of your infrastructure. It's typically ignored in version control systems.

- **providers.tf**: Configuration file for specifying provider credentials and settings.

- **modules/**: A directory for organizing reusable modules, which help keep your code DRY  and promote code reuse.


---

# **Sample Terraform Configuration**
Here's a basic example of a Terraform configuration file that provisions an AWS EC2 instance:

```hcl
# Specify the AWS provider and credentials
provider "aws" {
  region     = "us-east-1"
  access_key = "YOUR_ACCESS_KEY"
  secret_key = "YOUR_SECRET_KEY"
}

# Define an EC2 instance resource
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

# Output the public IP address of the EC2 instance
output "instance_public_ip" {
  value = aws_instance.example.public_ip
}
```
In this example:

- We specify the AWS provider and credentials.
- We define an EC2 instance resource with its configuration.
- We create an output to display the public IP address of the instance.

To use this configuration, follow these steps:


1. Install Terraform on your system.
2. Create a `main.tf` file with the above configuration.
3. Initialize the working directory using `terraform init`.
4. Review the planned changes using `terraform plan`.
5. Apply the configuration to create the resources with `terraform apply`.
6. Retrieve the outputs using `terraform output`.

Terraform will handle the provisioning and management of the EC2 instance based on your configuration.


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

# **Other Terraform info**
#### Terraform.rc file
A Terraform RC file, or  `terraform.rc`  file, is a configuration file that allows you to set default options and behaviors for the Terraform CLI (Command-Line Interface). It helps you define persistent CLI settings, such as environment variables and CLI configuration flags, which are automatically loaded when you run Terraform commands.

The  `terraform.rc`  file is typically placed in the user's home directory (e.g.,  `~/.terraformrc`  on Unix-based systems or  `%APPDATA%\terraform.rc`  on Windows). It uses the HCL (HashiCorp Configuration Language) syntax to define the configuration options.

With the  `terraform.rc`  file, you can set various configurations, such as default provider configurations, backend configurations, and environment variables. It allows you to customize and streamline your Terraform workflow by avoiding repetitive command-line options or environment variable settings.

Here's an example of a  `terraform.rc`  file:

```
provider_installation {
  filesystem_mirror {
    path    = "/path/to/provider/mirror"
    include = ["registry.terraform.io/*"]
  }
}

credentials "app.terraform.io" {
  token = "YOUR_API_TOKEN"
}

environment_variables {
  TF_LOG = "DEBUG"
}
```

In this example, the  `provider_installation`  block specifies a local provider mirror path. The  `credentials`  block sets an API token for the  `app.terraform.io`  registry. The  `environment_variables`  block sets the  `TF_LOG`  environment variable to  `DEBUG`  for verbose logging.

Note that the specific configuration options and syntax may vary depending on the Terraform version you are using. It's recommended to refer to the official Terraform documentation for the version you are working with to understand the available configurations and their usage.

...in progress