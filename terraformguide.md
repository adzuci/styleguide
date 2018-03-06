# Terraform Style Guide

This is intended to be a guide of Terraform syntax and general best practices.
 
As Terraform utilises [HCL](https://github.com/hashicorp/hcl), you may wish to take a detailed look at its
[syntax guide](https://github.com/hashicorp/hcl/blob/master/README.md#syntax).

Forked from [Mike Brannigan](https://gist.github.com/mbrannigan) and inspired by [The Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide) and
[The Puppet Style Guide](https://docs.puppetlabs.com/guides/style_guide.html).

In October Hashicorp released there own [guide](https://www.terraform.io/docs/enterprise/guides/recommended-practices/index.html), which focuses
on Terraform Enterprise and includes several other opinions about provisioning practices.

## Table of Contents

* [Code Layout](#code-layout)
* [Modules](#modules)
* [Variables](#variables)
* [Outputs](#outputs)
* [Comments](#comments)
* [Labels](#labels)

## Code Layout

Indentation should be 2 spaces (soft tabs). No hard tabs.

Attribute assignments (`=`) should be aligned for clarity.

```hcl
// bad
resource "aws_security_group" "main" {
    name = "${var.name}"
    description = "Security Group ${var.name}"
    vpc_id = "${var.vpc_id}"
    tags {
        Name = "${var.name}"
    }
}

// good
resource "aws_security_group" "main" {
  name        = "${var.name}"
  description = "Security Group ${var.name}"
  vpc_id      = "${var.vpc_id}"
  tags {
    Name = "${var.name}"
  }
}
```

## Modules

## Variables

Variables should be provided in a `variables.tf` file at the root of your module.

A description and type should be provided for each declared variable.

When it makes sense, please provide a reasonable default value.

There should be two blank lines between resource definitions.

```hcl
// bad
variable "vpc_id" {}

// good
variable "vpc_id" {
  description = "The VPC this security group will go in"
  type        = "string"
}


variable "public_subnet_ids" {
  description = "List of public subnet IDs"
  type        = "list"
  default     = []
}
```

Variable names should be descriptive. Avoid abbreviations when reasonable.  Verbosity is a good thing.

```hcl
// bad
variable "pub_sub_id" {}

// good
variable "public_subnet_id" {
  // definition
}
```

Boolean variables should follow the idiomatic pattern `is_<condition>`

```hcl
// bad
variable "public" {}

// bad
variable "is_public" {
  description = "Is the subnet public?"
  type        = "string"
  default     = false
}
```

Terraform resources usually have an ID or some other attribute associated with them.  Variables for modules
should refer to these attributes and match the actual name as much as possible.

```hcl
// bad
variable "vpc_cidr" {}
// -- or --
variable "cidr_block" {}
// -- or --
variable "address_range" {}


// good
variable "vpc_cidr_block" {
  // the vpc module exports an attribute named "cidr_block"
  description = "The CIDR block associated with the VPC"
  type = "string"
}
```

If the variable has a particular unit of measure, feel free to add that in for additional clarity.

```hcl
// bad
variable "root_disk_size" {}

// good
variable "root_disk_size_in_gb" {
  // definition
}
```

Variable names for lists should somehow indicate that they are a group of things.  You know... plural.

```hcl
// bad
variable "public_subnet_id" {}

// good
variable "public_subnet_ids" {
  // definition
}
```

## Outputs

Outputs should be provided in an `outputs.tf` file at the root of your module.

Attributes exported by your modules should follow the same descriptive format as variable names.

## Comments

Only comment what is necessary.

Single line comments: `#` or `//`.

Multi-line comments: `/*` followed by `*/`.

## Labels
All resources should have the following labels assigned to them

| label | description |
|-------|-------------|
| Name  | Descriptive name for the resource |
| Environment | The environment for the resource - value is usually the name of the child directory under the `./environments/` directory |
| Application | The application supported by this resource.  VPC and network resources are assigned the value `infrastructure` |
| ManagedBy | Defaults to `terraform` |