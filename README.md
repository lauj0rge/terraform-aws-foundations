[![Terraform Version](https://img.shields.io/badge/terraform-≥1.5-blue?logo=terraform)](https://github.com/hashicorp/terraform)
[![AWS](https://img.shields.io/badge/AWS-Landing%20Zone-orange?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/landing-zone/)
[![Infrastructure as Code](https://img.shields.io/badge/IaC-Terraform%20Modules-623CE4?logo=terraform)](https://www.terraform.io/)
[![KMS Encrypted](https://img.shields.io/badge/Security-KMS%20Enabled-green)](https://aws.amazon.com/kms/)
[![Multi-Account](https://img.shields.io/badge/Architecture-Multi--Account-yellow)](https://aws.amazon.com/organizations/)
[![CI](https://img.shields.io/github/actions/workflow/status/lauj0rge/terraform-aws-foundations/ci.yml?label=CI%20Build)](https://github.com/lauj0rge/terraform-aws-foundations/actions)

# Terraform AWS foundations

A **modular Terraform blueprint** to bootstrap a secure AWS environment with best-practice defaults.
It provisions essential foundations—VPC, subnets, IAM roles, S3 logging, CloudTrail, and AWS Organizations—so you can start from a clean, reproducible base across multiple accounts or environments.

> ✅ Compatible with Terraform ≥ 1.5
> ☁️ AWS multi-account ready
> 🔒 Security-first defaults
> 🧩 Modular and extensible

---

## Overview

`terraform-aws-foundations` is a simplified fork inspired by the [AWS Landing Zone](https://aws.amazon.com/solutions/aws-landing-zone/) concept.
It removes vendor-specific tooling and focuses on portable Terraform code that deploys directly into your AWS account.

### Key Capabilities

* Creates or connects to an AWS **Organization** and baseline accounts.
* Provisions a **VPC**, subnets, NAT gateways, and routing tables.
* Enables **CloudTrail**, **Config**, and **S3 bucket logging**.
* Bootstraps secure **IAM roles and policies** (including cross-account access).
* Supports isolated **dev/prod environments** via `.tfvars` overlays.

---

## Repository Structure

| Path                   | Purpose                                              |
| ---------------------- | ---------------------------------------------------- |
| `modules/organization` | AWS Organizations, OUs, accounts                     |
| `modules/network`      | VPC, subnets, routes, gateways                       |
| `modules/security`     | IAM roles, policies, cross-account access            |
| `modules/logging`      | CloudTrail, Config, S3 logging setup                 |
| `examples/`            | Minimal examples showing per-env use (`dev`, `prod`) |
| `env/*.tfvars`         | Environment definitions                              |
| `main.tf`              | Root composition for all foundation modules          |

---

## Usage

```hcl
module "foundations" {
  source = "github.com/lauj0rge/terraform-aws-foundations"

  organization_enabled = true

  vpc_cidr_block       = "10.0.0.0/16"
  vpc_subnets          = ["10.0.1.0/24", "10.0.2.0/24"]

  enable_cloudtrail    = true
  enable_config        = true
  create_iam_baseline  = true
}
```

Then apply:

```bash
terraform init
terraform apply -var-file=env/dev.tfvars
```

Outputs include:

* `vpc_id`
* `iam_admin_role_arn`
* `cloudtrail_bucket`
* `organization_id`

---

## Example `env/dev.tfvars`

```hcl
region                  = "eu-west-1"
vpc_cidr_block          = "10.10.0.0/16"
enable_cloudtrail       = true
create_iam_baseline     = true
aws_account_email_admin = "admin@example.com"
```

---

## Security and Access

* Deploy using an IAM role with permissions for Organizations, IAM, EC2, and S3.
* All resources are tagged automatically (`Environment`, `Owner`, `CostCenter`).
* Sensitive outputs are marked as `sensitive = true` in Terraform.

---

## Extend This Blueprint

You can easily add:

* `modules/dns` → Route 53 zones, resolver rules.
* `modules/firewall` → AWS Network Firewall, centralized logging.
* `modules/backup` → AWS Backup plans for baseline protection.

Follow the same pattern: define variables, outputs, and register in `main.tf`.


