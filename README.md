## Purpose

This module is considered to be a [data-only](https://www.terraform.io/docs/language/modules/develop/composition.html#data-only-modules) module. Given the name of a VPC and an optional set of availability zones, this module returns information about a VPC, such as public and private subnets, the VPC ID, etc. See the [outputs](outputs.tf) file for which data is returned from this module. This module is useful for workspaces that require such information without declaring repetitive `data` sources in your Terraform configurations.

## Usage

The following example creates a security group and an application load balancer.

```hcl
provider "aws" {}

module "networking" {
  source  = "github.com/mongodb-ansible-roles/terraform-aws-networking-readonly"
  version = "1.1.0"

  vpc_name = "tutorial-vpc"
}

resource "aws_security_group" "this" {
  ingress = [
    {
      cidr_blocks = ["0.0.0.0/0"]
      from_port   = 443
      protocol    = "TCP"
      to_port     = 443
    }
  ]

  vpc_id = module.networking.vpc_id
}

resource "aws_lb" "this" {
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.this.id]
  subnets            = module.networking.public_subnets
}
```

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| aws | ~> 3.64.2 |

## Providers

| Name | Version |
|------|---------|
| aws | ~> 3.64.2 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| availability\_zones | Select subnets only in the given AZs | `set(string)` | `[]` | no |
| vpc\_name | The name of the VPC | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| dns\_hostnames\_enabled | Indicates if instances launched in this VPC will have public DNS hostnames |
| dns\_support\_enabled | Indicates if DNS support is enabled for this VPC |
| private\_subnets | List of private subnets in this VPC |
| public\_subnets | List of public subnets in this VPC |
| vpc\_arn | Arn of this VPC |
| vpc\_cidr\_block | CIDR range for this VPC |
| vpc\_id | The ID of the VPC |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## License

[MIT License](LICENSE)
