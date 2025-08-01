### Terraform Blocks
- Terraform has 6 primary types of blocks used in configurations:
| Block Type   | Purpose |
|--------------|---------|
| **provider** | Defines the cloud/service provider and its configuration (e.g., AWS, Azure, GCP). |
| **resource** | Declares infrastructure resources to create (e.g., EC2, VPC, S3). |
| **variable** | Defines input variables for flexibility and reusability. |
| **output** | Displays values after `terraform apply` (e.g., instance IPs, IDs). |
| **module** | Calls external or local modules to reuse grouped configurations. |
| **data** | Fetches read-only data from providers (e.g., existing AMI ID or VPC). |

- Additionally, there are some supporting blocks:
| Block Type   | Purpose |
|--------------|---------|
| **locals** | Defines local constants or expressions for internal use. |
| **terraform** | Configures Terraform settings, backend, and required providers. |
| **provisioner** | Runs scripts or commands on resources after creation. |
| **lifecycle** | Manages resource behavior (e.g., prevent destroy, create before destroy). |     |

```
# 🔷 terraform block
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  required_version = ">= 1.0"
}

# 🔷 provider block
provider "aws" {
  region = "ap-south-1"
}

# 🔷 variable block
variable "instance_type" {
  description = "EC2 instance type"
  default     = "t2.micro"
}

# 🔷 locals block
locals {
  instance_name = "demo-instance"
}

# 🔷 data block
data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# 🔷 resource block
resource "aws_instance" "web" {
  ami           = data.aws_ami.latest_amazon_linux.id
  instance_type = var.instance_type
  tags = {
    Name = local.instance_name
  }

  # 🔷 provisioner block
  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install -y httpd",
      "sudo systemctl start httpd"
    ]
  }

  # 🔷 lifecycle block
  lifecycle {
    prevent_destroy = true
  }
}

# 🔷 module block (example usage)
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "5.1.0"

  name = "demo-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["ap-south-1a"]
  public_subnets  = ["10.0.1.0/24"]
  enable_dns_hostnames = true
}

# 🔷 output block
output "instance_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.web.public_ip
}
```
---
## Terraform Commands and Generated Files
| Terraform Command                       | Generated File                                    | Purpose                                                                                                 |
| --------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `terraform init`                        | `.terraform/` directory and `.terraform.lock.hcl` | Initializes working directory, downloads provider plugins, and creates lock file for provider versions. |
| `terraform plan -out=plan.out`          | `plan.out`                                        | Saves the execution plan to a file for review or later apply.                                           |
| `terraform apply`                       | `terraform.tfstate`                               | Creates or updates the state file storing the current infrastructure state.                             |
| `terraform apply` (with previous state) | `terraform.tfstate.backup`                        | Creates a backup of the previous state before applying changes.                                         |
| `terraform refresh`                     | Updates `terraform.tfstate`                       | Updates the state file with real infrastructure state without changing resources.                       |
| `terraform output`                      | Reads from `terraform.tfstate`                    | Does not generate a file; reads output values from the state file.                                      |
---