## 1. What is IaC?
Infrastructure as Code means setting up and managing infrastructure using code instead of doing it by hand. The reason it’s used is because it saves a lot of time, reduces mistakes, and keeps all environments consistent. It works by writing a configuration file that describes what kind of setup you want — like your servers or databases — and tools such as Terraform automatically create that setup for you. So, it makes managing infrastructure as easy and reliable as writing software code.

## 2. What is Terraform?
Terraform is a tool that allows you to manage infrastructure using code rather than doing everything manually. It’s used because it automates the setup, reduces human errors, and ensures that the same infrastructure can be recreated easily across different environments. It works by writing configuration files that describe what resources you need, and Terraform reads those files to automatically create, update, or delete the infrastructure as needed. This makes managing cloud resources fast, consistent, and reliable.

Terraform lifecycle has three stages:  
1. Terraform init
2. Terraform plan
3. terraform apply

## 3. What are Providers?
In Terraform, providers are like plugins that let Terraform connect and work with different platforms such as AWS, Azure, or Google Cloud. We use providers because Terraform itself can’t directly communicate with these platforms — the provider acts as a bridge that tells Terraform how to create and manage resources there. It works by declaring the provider in the configuration file, and when we run terraform init, Terraform automatically downloads and sets up that provider. After that, it can use the provider to manage the resources we define in our code.

**What are different ways to configure providers in terraform?**  
1. **Root Module:** Configuring providers in the root module gives global settings like region and authentication that apply to the whole Terraform project.  
2. **Child Module:** Configuring providers inside a child module lets that module use its own provider settings or aliases — useful for multi-account or multi-region deployments.   
3. **required_providers block:** Using the required_providers block declares which providers and versions Terraform should download, acting like dependency management, while the real configuration still happens in the provider blocks.

## 4. What is multiple providers?
**What “multiple providers” means** is that in Terraform you can use more than one provider in the same project. **Why this matters** is because real-world infrastructure rarely lives in one place — a company might host some services on AWS, others on Azure, and manage DNS via Cloudflare — so Terraform needs to control all of them at once.

## 5 What is Multi Region?
In Terraform, multi-region means setting up your infrastructure in more than one cloud region, like having some servers in Mumbai and others in Singapore. It’s done to make your system more reliable and faster — if one region fails, the other keeps running, reduces latency for users in different locations. It works by defining multiple provider blocks in Terraform, each pointing to a different region. Terraform then uses those providers to create resources across all the regions you’ve mentioned in your configuration.
```
provider "aws" {
  alias = "us-east-1"
  region = "us-east-1"
}

provider "aws" {
  alias = "us-west-2"
  region = "us-west-2"
}

resource "aws_instance" "example" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  provider = "aws.us-east-1"
}

resource "aws_instance" "example2" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  provider = "aws.us-west-2"
}
```

**So do i need to write resources multiple times for different regions or i will write it once and it will automtically be copied to all the regions?**  
What happens is Terraform will NOT automatically copy a resource to multiple regions — if you want the same resource in two regions, you must either write the resource block twice or use a reusable module/loop to generate multiple instances.

You can duplicate the same resource and assign different provider aliases manually, OR you can write the resource once inside a module and pass different providers to it, OR use for_each to auto-create copies — but in all cases Terraform requires clear definition, it will not clone your resources magically across regions by default.

## 6. What is Multi Cloud?
Multi-cloud means using more than one cloud platform at the same time — like combining AWS, Azure, and Google Cloud based on your needs. It’s done mainly to avoid relying on a single provider, improve reliability, and make use of each cloud’s strengths. In Terraform, this is very easy to manage because you can define multiple providers in the same project, and Terraform will automatically create and manage resources across all those clouds using one set of configuration files. This gives full control over different cloud environments from a single tool.

```
provider "aws" {
  region = "us-east-1"
}

provider "azurerm" {
  subscription_id = "your-azure-subscription-id"
  client_id = "your-azure-client-id"
  client_secret = "your-azure-client-secret"
  tenant_id = "your-azure-tenant-id"
}
```
```
resource "aws_instance" "example" {
  ami = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
}

resource "azurerm_virtual_machine" "example" {
  name = "example-vm"
  location = "eastus"
  size = "Standard_A1"
}
```

*Note: Multi-provider just means using more than one provider in Terraform, which could even be multiple configurations of the same provider (like AWS in two regions). Multi-cloud specifically means using different cloud platforms (like AWS + Azure + GCP) at the same time.*

## 7. What are variables?
In Terraform, variables are used to store values like region names, instance types, or project names so that we don’t have to repeat them everywhere in the code. They make our configuration flexible and easier to maintain. For example, if we want to change a region or instance type, we just update the variable once instead of editing multiple files. We usually define variables in a variables.tf file and assign values in a terraform.tfvars file or from the command line. This helps keep the code clean, reusable, and easy to manage across different environments.

## 8. What are conditions?
Conditions in Terraform are used to make decisions within our code, similar to an if–else statement. They help us choose different values or configurations based on certain situations. For example, we can say — if the environment is production, use a bigger instance type; otherwise, use a smaller one. Terraform uses a simple ternary operator to do this, which looks like a short if–else expression. This makes the setup smarter, because Terraform automatically adjusts the configuration based on the condition we define.

## 9. What are Modules?
In Terraform, modules are like reusable blocks of code that help organize and simplify your setup. Instead of writing the same Terraform configuration multiple times, you can define it once as a module and use it anywhere. It’s mainly used to make the code cleaner, more modular, and easier to maintain. A module usually contains files like main.tf, variables.tf, and outputs.tf. We can then call that module from our main Terraform file using a module block. This approach saves time, avoids duplication, and keeps the infrastructure setup more consistent and manageable.

## 10. What is Terraform statefile?  
A Terraform state file is basically Terraform’s memory.
It stores everything Terraform has already created—like VPC IDs, subnet IDs, EC2 instance IDs, and the current condition of your infrastructure.

Terraform uses this state file to understand what exists on the cloud so it can decide what to add, update, or delete. If I change something in my code, Terraform compares it with the state file and then applies only the required updates instead of recreating everything from scratch.

In short, the Terraform state file helps Terraform remember what it built, avoid mistakes, plan changes correctly, and keep infrastructure consistent.

## 11. What is Terraform Statefile Management?  
Terraform statefile management means handling the Terraform state file in a safe, secure, and organized way so Terraform always knows the correct condition of your infrastructure.Instead, we store it in a **remote backend** like AWS S3, along with **state locking** through DynamoDB to prevent two people from editing the state at the same time.

## 12. What is Terraform remote backend using S3 bucket?
A Terraform remote backend using an S3 bucket simply means storing your Terraform statefile in an AWS S3 bucket, which acts like a central storage location and one single source of truth for anyone who is working on a same infrastructure.
Terraform reads from this S3 file, updates it, and keeps everything consistent.
We usually combine S3 with DynamoDB for state locking, so only one person can modify the state at a time, preventing conflicts.

## 13. What is Terraform state locking using DynamoDB?
Terraform state locking using DynamoDB is a way to make sure that only one person or one process can modify the Terraform statefile at a time.

Whenever Terraform runs, it creates a lock entry in DynamoDB telling everyone else:
“Someone is already working — please wait.”

Once the apply/plan finishes, Terraform removes the lock, and others can continue safely.

In short, DynamoDB state locking protects the Terraform statefile from being changed by two people at the same time, ensuring safety, consistency, and reliability of your infrastructure.

## 14. What are Provisioners?
In Terraform, provisioners are used to run scripts or commands on a resource after it’s created.
They’re mainly used for configuration tasks — for example, installing software, setting up environment variables, or copying files to a new server.

There are different types like remote-exec (to run commands on the resource), local-exec (to run commands on your local system), and file (to copy files).

However, provisioners are considered a last resort — HashiCorp recommends using tools like Ansible or Packer for configuration management instead.
Provisioners are best for small setup steps that can’t be done directly through Terraform resources.

## 15. What are Terraform Workspaces?
Terraform Workspaces let us manage multiple environments — like dev, staging, and production — using the same codebase but with separate state files.

Each workspace maintains its own state file, so any changes in one workspace don’t affect the others.

Workspaces are helpful for testing and environment separation, but for larger projects, teams often use separate backends or folders for even clearer isolation.
Still, they’re great for small-to-medium setups or quick environment management.

## 16. What is Secrets Management?
Secrets management in Terraform means securely handling sensitive information like passwords, API keys, and tokens that are required during infrastructure provisioning.
Instead of hardcoding secrets inside Terraform files or variables, we store them in a secrets manager such as HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault.

Terraform then fetches these secrets dynamically and securely at runtime.
This approach helps maintain security, compliance, and automation, ensuring that secrets are never exposed in version control or Terraform state files.

## 17. What is Terraform Vault Integration?
Terraform integrates with HashiCorp Vault to securely manage and access secrets like API keys, passwords, or tokens during infrastructure deployment.
Instead of hardcoding credentials in Terraform files, Terraform uses the Vault provider to fetch secrets dynamically at runtime.

This makes infrastructure both secure and automated — Vault handles secret rotation and encryption, while Terraform focuses on building resources.
For example, when provisioning a database, Terraform can directly pull the username and password from Vault without ever exposing them in the state or code.

In short, Terraform + Vault is a powerful combination that brings security, compliance, and automation together in Infrastructure as Code.



Idempotency