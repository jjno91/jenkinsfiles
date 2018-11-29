# jenkinsfiles

Catalog of useful Jenkinsfiles

## Overview

The Jenkinsfiles in this repository use a Kubernetes cluster as the build agent, but the agent can be reconfigured to another target without affecting most other features.

## Usage

These Jenkinsfiles are designed to be usable as-is. In most cases all you need to do is copy the desired Jenkinsfile to your repository and remove the dot prefix leaving the name `Jenkinsfile` and Jenkins pipeline based jobs will automatically pick up the file as your job configuration.

<https://jenkins.io/doc/book/pipeline/>

## docker.Jenkinsfile

Docker build, tag, and push with tagging based off of git commit ID.

## kubectl.Jenkinsfile

Kubectl apply Kubernetes YAML files to the same cluster that Jenkins is running in. Requires a pre-existing `jenkins` service account on the target cluster and by default looks for Kubernetes YAMLs in a directory named `kubernetes`.

## terraform-disabled.Jenkinsfile

Destroy arbitrary Terraform infrastructure using AWS credentials loaded through the Jenkins credential store. Useful to replace the complementary terraform.Jenkinsfile when a Terraform managed environment is being decommissioned.

## terraform-state-destroyer.Jenkinsfile

Allows you to target and destroy the infrastructure contained in arbitrary Terraform state files. Only supports S3 backends in its current state, but can easily be modified.

In order for this Jenkinsfile to work properly you will still need to define a provider and backend in Terraform code before executing.

## terraform.Jenkinsfile

Apply arbitrary Terraform code using AWS credentials loaded through Jenkins credential store.
