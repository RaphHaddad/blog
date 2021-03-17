---
title: 'Bicep vs Terraform'
date: 2021-03-17T00:00:00.000+10:00
draft: false
tags : [agile, azure devops, Infrastructure as Code]
---

Microsoft recently released a production-ready version of Bicep.
An infrastructure language that compiles down to ARM Templates that can
subsequently be deployed into Azure. Bicep deployment is supported in the Azure
CLI as a native infrastructure language and all the compilation happens in
the background.

```cmd
% az deployment group create --template-file ./main.bicep -g an-example-resource-group
```

In addition, the tooling is maturing rapidly across the development ecosystem.
Similarly, Terraform is a mature framework has support from Microsoft and other
cloud vendors(https://docs.microsoft.com/en-us/azure/developer/terraform/).

This post will be a comparison of both these technologies that will help the
reader in deciding which technology to use.

!["Bicep vs Terraform"](/images/bicep-vs-terraform.png "Bicep vs Terraform")

## Syntax

Both Bicep and Terraform utilise a propriety custom-build syntax to define
cloud infrastructure. The syntax is custom-build for infrastructure unlike
JSON or other programming languages.

Both are quite similar, however, there are differences.

Here is the Terraform code for declaring a storage account on Azure:

```terraform
resource "azurerm_storage_account" "a-storage-account" {
  name                     = "anexamplestorageaccount
  resource_group_name      = azurerm_resource_group.resource-group.name
  location                 = azurerm_resource_group.resource-group.location
}
```

Here is the equivalent Bicep code

```bicep
resource symbolicname 'Microsoft.Storage/storageAccounts@2019-04-01' = {
    name: "anexamplestorageaccount"
    location: resourceGroup.location
}
```

One of the biggest advantage of both is the symbolic links that each generate.
This makes hooking into properties that are generated after provisioning much
easier than ARM templates. For example: constructing a storage SQL Storage
string to store on an App Service configuration. When declaring a new resource,
both Bicep and Terraform will provision any referenced resource before the new resource.

One difference between the two syntaxes is how to deal with hierarchical
resources. Terraform deals with this by referencing one resource to another
via properties.

For example, the below provisions a SQL Server with a Database in Terraform:

```terraform
resource "azurerm_sql_server" "a-server" {
    name                         =  "a-sql-server123"
    resource_group_name          = "azurerm_resource_group.resource-group.name"
    location                     = azurerm_resource_group.resource-group.location
    version                      = "12"
    administrator_login          = "acooladmin"
    administrator_login_password = "thisIsDog11"

}

resource "azurerm_sql_database" "a-database" {
    name                = "a-database-123"
    resource_group_name = azurerm_resource_group.resource-group.name
    location            = azurerm_resource_group.resource-group.location
    server_name         = azurerm_sql_server.a-server.name
}
```

The property `server_name` on the database is assigned to the server
`a-server.name`.
This is how Terraform knows that one resource belongs (or is a child) to another
through the symbolic links.

On the other hand, Bicep deals with child resources a bit differently through the name of the actual resource to be provisioned. For example

```Bicep
resource sqlServer 'Microsoft.Sql/servers@2019-06-01-preview' = {
  name: ‘a-sql-server123’
  location: resourceGroup().location
  properties: {
    minimalTlsVersion: '1.2'
    administratorLogin: ‘acooladmin’
    administratorLoginPassword: ‘thisIsDog11’
  }
}

resource sqlDatabase 'Microsoft.Sql/servers/databases@2020-08-01-preview' = {
  name: '${sqlServer.name}/a-database-123'
  location: resourceGroup().location
}
```

Bicep designates a hierarchy based on the name of a resource. A nested resource
is declared via the parent’s name followed by a `/` and the child resource’s
name. Luckily because of Bicep’s ease of string concatenation and symbolic
links, this can be easily achieved.

## Ease of Learning

Both syntaxes are easy to learn due to their simplicity. Bicep, being natively
supported through the Azure CLI makes deploying straight into Azure easy and
Terraform with its own CLI and awesome documentation also makes this possible.
Whilst both are simple to use locally, Bicep is easier than Terraform to start
to deploy from release pipelines immediately. The reason is that Terraform
requires a persistent storage to track resource modifications. This state
requires extra setup on release pipelines but comes with some advantages that
will be mentioned further on.

## Deployment Methods

Bicep templates are similar to ARM Templates in that they are incremental.
That is, resources are incrementally added to their targets (resource groups
or subscriptions) and if a resource is deleted from a template and the template
is redeployed, then the code-deleted resource won’t be deleted on Azure Cloud.

Terraform deploys differently, the state on Azure Cloud must match exactly
what is on the code (exact state). That is, if a resource is deleted on the
code templates, they will be deleted on the target. This is one reason (amongst
many) why Terraform needs its own persistent storage to store state.

Both these deployment methods have their advantages and disadvantages.
The advantages of having incremental deployment means a reduced risk of
accidental deletion, however, a higher chance of configuration drift occurring
between templates and the deployed infrastructure on Azure Cloud.

The advantage of exact state means less configuration drift, cleaner
templates, and deletion as a valid operation on resources. The disadvantages
are that state may hold sensitive information like passwords (please
see [this Terraform
documentation](https://www.terraform.io/docs/language/state/sensitive-data.html)
that explains the importance of treating state as sensitive data) and a higher
risk of accidental deletion of resources (albeit can be reduce by using the
[lifecycle meta-argument](https://www.terraform.io/docs/language/meta-arguments/lifecycle.html)). 

Due to Terraform’s ability to manage state, the executor of a Terraform template
can review changes prior to them being deployed into an environment. This means
that issues can be captured further left in the deployment lifecycle.

## Multiplatform Support

Bicep is only supported by Azure Cloud, however, Terraform supports a variety
of platforms including (amongst others): AWS, GCP, and Azure DevOps. Using
Terraform across platforms is a pleasant experience because of the universal
syntax across all target platforms and the ability to hook into variables
from one platform to another using Terraform’s symbolic naming of resources.
This can be advantageous if a particular workload on one cloud platform needs
to integrate to a workload on another cloud platform.

## Commercial and Community Support

Both have extensive community and commercial support. Terraform is backed by
Hashicorp commercially and Microsoft is supports it through various means such
as documentation and tutorials. Bicep is backed by Microsoft commercially and
is natively supported by the Azure CLI and PowerShell Azure cmdlets. Both are
open source and allow changes to various artifacts.

I hope you enjoyed this post and thank you for reading.
