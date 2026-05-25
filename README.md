# EC2-server-with-SSH-access-on-terraform
# Terraform AWS EC2 Deployment with NGINX

This project provisions an AWS EC2 instance using Terraform and configures it with an Amazon Linux 2023 AMI, custom security group rules, SSH access, and an NGINX web server.

---

# Technologies Used

* Terraform
* AWS EC2
* AWS VPC
* AWS Security Groups
* Amazon Linux 2023
* NGINX
* SSH

---

# Project Structure

```text
.
├── main.tf
├── variables.tf
├── provider.tf
└── outputs.tf
```

---

# Infrastructure Overview

The Terraform configuration performs the following tasks:

* Uses the default AWS VPC and subnet
* Fetches the latest Amazon Linux 2023 AMI
* Creates a security group allowing:

  * HTTP traffic on port 80
  * SSH access on port 22 from a specific IP
* Launches a `t3.micro` EC2 instance
* Associates a public IP address
* Uses an imported SSH key pair for authentication
* Outputs the instance ID and IP addresses

---

# Provider Configuration

```hcl
provider "aws" {
  region = var.aws_region
}
```

Default region:

```hcl
ap-south-1
```

---

# EC2 Configuration

```hcl
resource "aws_instance" "ec2" {
  ami                         = data.aws_ami.amazon_linux.id
  instance_type               = var.instance_type
  subnet_id                   = data.aws_subnets.default.ids[0]
  vpc_security_group_ids      = [aws_security_group.web_sg.id]
  key_name                    = "id_ed25519"

  associate_public_ip_address = true
}
```

---

# Security Group Rules

| Port | Protocol | Purpose     |
| ---- | -------- | ----------- |
| 22   | TCP      | SSH Access  |
| 80   | TCP      | HTTP Access |

SSH access is restricted to a single public IP address.

---

# Deployment Steps

## Initialize Terraform

```bash
terraform init
```

## Preview Infrastructure

```bash
terraform plan
```

## Deploy Infrastructure

```bash
terraform apply
```

---

# Import SSH Key to AWS

```bash
aws ec2 import-key-pair \
  --key-name id_ed25519 \
  --public-key-material fileb://id_ed25519.pub
```

---

# Connect to EC2 Instance

```bash
ssh -i ~/.ssh/id_ed25519 ec2-user@<PUBLIC_IP>
```

---

# Install NGINX

```bash
sudo dnf update -y
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

# Outputs

Terraform outputs:

* Instance ID
* Public IP
* Private IP

---

# Result

A fully functional Amazon Linux EC2 instance running NGINX was successfully deployed on AWS using Terraform.
