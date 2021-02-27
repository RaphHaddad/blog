---
title: 'DevSecOps on Azure using Terraform and Python'
date: 2021-02-28T00:00:00.001+11:00
draft: false
tags : [azure devops, azure cloud, devops]
---

This post will demonstrate how to create a segregated Azure Cloud environment (a
resource group and service
connection from Azure DevOps) that can be used by development teams to provision their own infrastructure and
deploy their compiled code. All in an automated and secure manner.

Teams can then setup their own release pipelines using the provisioned resource
group and service connection. The service connection created will only have
permission to a single resource group within Azure.

The reference code for this post is available on this [GitHub repository](TODO).

Specifically, this post will demonstrate:

- How to provision a resource group for an application or service; and
- How to create a service connection from Azure DevOps to Azure Cloud
with [contributor](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#contributor) access to that
  resource group.

The above will be done with [Terraform](https://www.terraform.io) wrapped in
a [Python](https://www.python.org) script.

## Pre-prerequisites and Setup

In order to automate the above process Terraform has two providers: an [Azure RM
provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest) and
an [Azure
DevOps](https://registry.terraform.io/providers/microsoft/azuredevops/latest/docs)
provider. Both of these will be utilised in order to create a resource group
with a service connection.

The first step is authenticating to an Azure DevOps instance and an Azure Cloud
Subscription. To authenticate to Azure Cloud the [Azure
CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) is needed.

```cmd
% az login
The default web browser has been opened at https://login.microsoftonline.com/common/oauth2/authorize. Please continue the login in the web browser. If no web browser is available or if the web browser fails to open, use device code flow with `az login --use-device-code`.
```

To authenticate into Azure DevOps. A [Personal Access Token (PAT)](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page) is needed with
the scope *Read, query, & manage* on *Service Connections*. After generating the
PAT, Terraform requires certain environment variables to be set:

```cmd
% export AZDO_PERSONAL_ACCESS_TOKEN={a-secure-access-token}
% export AZDO_ORG_SERVICE_URL=https://dev.azure.com/{a-secure-org}
```

## Initialising Terraform

Terraform relies on a state file to perform resource deployments, for the
purposes of this post, local system state will be used. For production
work-loads [remote state](https://www.terraform.io/docs/language/state/index.html) should be used.

The Python script [setup-environment.py](TODO), initialises Terraform state *dynamically* by assigning a
different state file according to the name of the resource group desired to be
provisioned. This means that the same script can be used for multiple resource
groups by changing the argument `--resource-group-name`.

For example: if the script is run with the below command:

```cmd
% ./setup-environment.py --resource-group-name "a-new-resource-group"  --azure-devops-project-name main
```

The resulting `terraform init` command will be

```cmd
% terraform init  -backend-config='path=.terraform-state-a-new-resource-group/a-new-resource-group.tfstate' -reconfigure --input=false
```

Terraform is able to dynamically change state via the `-backend-config` flag.

So, if the script is ran twice with two different resource groups like the below
snippet:

```cmd
% ./setup-environment.py --resource-group-name "a-new-resource-group" --azure-devops-project-name main
% ./setup-environment.py --resource-group-name "a-new-resource-group-2" --azure-devops-project-name main
```

Two folders will be created in the working directory:
`.terraform-state-a-new-resource-group` and
`.terraform-state-a-new-resource-group-2`. Both of which have a `tfstate` file.

This pattern can be achieved for any Terraform remote backend and will allow the
executor to use the same Terraform templates to create multiple instances of the
resources declared in the Terraform template.
*Note* it is important to treat Terraform state files as sensitive data
(passwords, tokens). See this [Terraform page](https://www.terraform.io/docs/language/state/sensitive-data.html?_ga=2.252783049.1925722553.1613943599-1447805869.1611735730#recommendations) for more information.

## Creating the Service Principle

A service principle is needed to create the service connection between Azure
DevOps and Azure Cloud. This service principle is provisioned with a randomly
generated password by Terraform, which will be used by the service connection.

The resource used to generate this is `random_password`. A snippet of the code is
shown below and the full code is available [on GitHub](TODO).

```hcl
resource "azuread_service_principal" "service-principle" {
  application_id               = azuread_application.ad-application.application_id
}

resource "azuread_service_principal_password" "service-principle-password" {
  service_principal_id = azuread_service_principal.service-principle.id
  description          = "Managed by Terraform"
  value                = random_password.service-principle-password.result
  end_date             = "2099-01-01T01:02:03Z"
}

resource "random_password" "service-principle-password" {
  length           = 16
  special          = true
  override_special = "_%@"
}
```

## Creating the Resource Group

The new resource group needs to be declared with the Terraform resource type `azurerm_role_assignment`
with `Contributor` access given to the previously declared [service principle](#creating-the-service-principle). The
snippet of the code is show below and the full code is available [on GitHub](TODO)

```hcl
resource "azurerm_resource_group" "resource-group" {
  name     = var.resource-group-name
  location = "Australia East"
}

resource "azurerm_role_assignment" "resource-group-service-principle-role-assignment" {
  scope                = azurerm_resource_group.resource-group.id
  role_definition_name = "Contributor"
  principal_id         = azuread_service_principal.service-principle.id
}
```

## Creating the Service Connection

The final resource to provision is the service connection from Azure DevOps to
Azure Cloud. At this stage, a resource group has been provisioned and a service
principle has been provisioned with a password. To create this service
connection the resource `azuredevops_serviceendpoint_azurerm` is used. Access to
the password is available as a resource attribute, so this can be used to
provision the service connection on Azure DevOps. The
snippet of the code is show below and the full code is available [on GitHub](TODO)

```hcl
resource "azuredevops_serviceendpoint_azurerm" "serviceendpoint-azure" {
  project_id            = data.azuredevops_project.project.id
  service_endpoint_name = "${var.resource-group-name}-azure-service-connection"
  description = "Managed by Terraform" 
  credentials {
    serviceprincipalid  = azuread_service_principal.service-principle.id
    serviceprincipalkey = random_password.service-principle-password.result
  }
  azurerm_spn_tenantid      = data.azurerm_subscription.current.tenant_id
  azurerm_subscription_id   = data.azurerm_subscription.current.subscription_id
  azurerm_subscription_name = data.azurerm_subscription.current.display_name
}
```

## Conclusion

The above setup creates a resource group and a service principle with
`Contributor` access to that resource group. It also creates a service
connection from Azure DevOps that uses the provisioned service principle that
can then be used in
release pipelines.

If the script is run as follows:

```cmd
% ./setup-environment.py --resource-group-name "an-example-resource-group" --azure-devops-project-name main
...
...
...
Apply complete! Resources: 7 added, 0 changed, 0 destroyed.
```

The result would be a resource group `an-example-resource-group` provisioned on Azure Cloud with
the service principle `an-example-resource-group-spn` having `Contributor`
access to that resource group.

![Resource Groups Provisioned with Terraform](/images/resource-group-terraform.png)

As well as, a service connection from Azure DevOps into Azure Cloud that can be
used by release pipelines.

![Resource Groups Provisioned with Terraform](/images/service-connection-terraform.png)

Finally, thank you for reading this post. I hope you got out of it something.
