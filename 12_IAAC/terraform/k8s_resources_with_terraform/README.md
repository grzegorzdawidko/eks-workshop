# Manage Kubernetes Resources via Terraform

Kubernetes (K8S) is an open-source workload scheduler with focus on containerized applications. You can use the Terraform Kubernetes provider to interact with resources supported by Kubernetes.

In this tutorial, you will learn how to interact with Kubernetes using Terraform, by scheduling and exposing a NGINX deployment on a Kubernetes cluster.

The final Terraform configuration files used in this tutorial can be found in the Deploy NGINX on Kubernetes via Terraform GitHub repository.


# Prereqs

You will need existing EKS cluster.


# Configure the provider

Before you can schedule any Kubernetes services using Terraform, you need to configure the Terraform Kubernetes provider.
In this tutorial we will use eks get-token to to this.
