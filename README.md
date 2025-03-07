# Docker Hub Provider

[![registry.terraform.io](https://img.shields.io/badge/terraform-docs-success)](https://registry.terraform.io/providers/BarnabyShearer/dockerhub/latest/docs)

![Build Status](https://github.com/BarnabyShearer/terraform-provider-dockerhub/actions/workflows/publish.yml/badge.svg)

Lifecycle management of Docker Hub using the v2 API.

This provider enables management of Docker Hub registries, groups, permissions and access tokens.

## Installation

The provider can be installed and managed automatically by Terraform. Sample `versions.tf` file:

```hcl
terraform {
  required_version = ">= 0.13"

  required_providers {
    dockerhub = {
      source  = "BarnabyShearer/dockerhub"
      version = ">= 0.0.15"
    }
  }
}
```

## Quick Start

```hcl
# Configure the Docker Hub Provider
provider "dockerhub" {
  username = "azurediamond"
  password = "hunter2"
}

# Create an organization group for developers
resource "dockerhub_group" "project-developers" {
  organisation = "organisation"
  name         = "project"
  description  = "Project developers"
}

# Create an organization group for CI
resource "dockerhub_group" "project-ci" {
  organisation = "organisation"
  name         = "ci"
  description  = "Project CI"
}

# Create an image registry
resource "dockerhub_repository" "project" {
  name             = "project"
  namespace        = "organisation"
  description      = "Project description"
}

# Associate our developers group with the registry
resource "dockerhub_repositorygroup" "project-developers" {
  repository = dockerhub_repository.project.id
  group = dockerhub_group.project-developers.group_id
  groupname = dockerhub_group.project-developers.name
  permission = "admin"
}

# Associate our CI group with the registry
resource "dockerhub_repositorygroup" "project-ci" {
  repository = dockerhub_repository.project.id
  group = dockerhub_group.project-ci.group_id
  groupname = dockerhub_group.project-ci.name
  permission = "write"
}
```

## Development Guide

If you wish to work on the provider, you'll first need [Go](http://www.golang.org) installed on your machine (version 1.12+ is _required_).
You'll also need to correctly setup a [GOPATH](http://golang.org/doc/code.html#GOPATH), as well as adding `$GOPATH/bin` to your `$PATH`.

To compile the provider, run `make terraform-provider-dockerhub`. This will build the provider and put the provider binary in the local directory.

### Testing

In order to test the provider, you can simply run `make test`.

```sh
make test
```

### Installing

To install the provider locally (for instance for manually testing) run

```sh
make install
```

This installs the provider to `~/.terraform.d/`, removes `.terraform.lock.hcl` and runs `terraform init` in the local folder.

## Thanks

Organisation and Group support added by [Magenta ApS](https://github.com/magenta-aps).
