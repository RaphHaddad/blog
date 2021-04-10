---
title: 'Azure CLI within PowerShell'
date: 2021-04-10T00:00:00.001+11:00
draft: false
tags : [DevOps, Azure, Azure DevOps, Cloud, PowerShell]
---

In a [previous post](/2021/03/python-to-automate-azure-deployments/),
I justified the usage of Python as a scripting language for Azure deployment automation.
One of the points made was the ease in which the Azure CLI can be used within
the Python ecosystem. In many scenarios though, the usage of Python may not be
appropriate due to existing technologies being used within a team or
organisation.

Today, I'll demonstrate how to use the Azure CLI within a PowerShell script,
but first, the justification for its usage.

## Azure Cmdlets vs Azure CLI within PowerShell

An initial assessment of the Azure CLI within a PowerShell script may not make
any sense. Specifically, if Microsoft has already written [Azure
Cmdlets](https://docs.microsoft.com/en-us/powershell/azure/?view=azps-5.7.0)
what reason is there to use the Azure CLI at all? Before this assessment is
made, below are a few reasons why one *should* use the cmdlets (amongst many):

1. They are created by Microsoft, as such, have commercial backing;
1. They are easy to import and are available on Azure DevOps build agents;
1. The cmdlets return static objects in code, meaning better tooling (like
   auto-complete); and
1. The cmdlets are easy to read as they are written as PowerShell-idiomatic
   [Verb-Noun pairs](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7.1).

Taking the above into consideration, why would one use the Azure CLI instead?

The Azure CLI provides many of the same advantages as the cmdlets. Specifically
points: 1 and 2. But perhaps the key advantage to using the Azure CLI is
debugging.

To demonstrate, if one wanted to get all the commands available in the Azure
Cmdlets, one would run (go ahead, run it, I **dare** you):

```PowerShell
> get-help Az
```

Running the above will give you a *nice* endless scroll as shown in the below
gif.

![Nice endless scroll](/images/get-help.gif "help")

This can be mitigated by using the wildcard character `*` to limit the relevant
cmdelts. For example: `get-help *StorageContainer`. However, this means that the 'Noun' part
of the cmdlet must be known ahead of time.

On the other hand, the Azure CLI is build with a `-h` flag for each command or
sub-command. Running `az -h` will return all sub-commands under the command
being queried, and running the
sub-command with the `-h` flag will return its sub-commands.

For example:

```cmd
% az -h

Group
    az

Subgroups:
    account : Manage Azure subscription information.
    acr     : Manage private registries with Azure Container Registries.
    ad      : Manage Azure Active Directory Graph entities needed for Role Based Access Control.
    advisor : Manage Azure Advisor.
    aks     : Manage Azure Kubernetes Services.

% az aks -h
Group
    az aks : Manage Azure Kubernetes Services.

Subgroups:
    nodepool   : Commands to manage node pools in Kubernetes kubernetes cluster.

Commands:
    browse     : Show the dashboard for a Kubernetes cluster in a web browser.
    check-acr  : Validate an ACR is accesible from an AKS cluster.
    create     : Create a new managed Kubernetes cluster.
    delete     : Delete a managed Kubernetes cluster.
```

During the development of an automation script, finding the relevant commands
quickly increases the efficiency of development. Here, the Azure CLI has
an added advantage over the cmdlets.

In addition to this, if an automation script is designed properly, the Azure CLI
provides an ability to preview commands prior to them being sent to Azure.
When using the cmdlets one would need to add a breakpoint on their execution, in
order to examine values that are being used.

This preview architecture for the Azure CLI can be achieved easily in PowerShell as follows:

```PowerShell
$commandToExecute = "az group list"
Write-Host "Executing..."
Write-Host "$commandToExecute"
Invoke-Expression "$commandToExecute"
```

## Show Me the Code Already!

All the code explained below is available on this [GitHub repository](https://github.com/RaphHaddad/powershell-azure-cli)

A PowerShell module named `AzCli.psm1` is available in the repository, it contains
only one function:

```PowerShell
function Invoke-AzCli($Command) {
    $toExecute = "az $Command"
    Write-Host "Executing:"
    Write-Host "$toExecute"
    Write-Host
    $result = Invoke-Expression "$toExecute"
    if ($LastExitCode -gt 0) {
        Write-Error $result
        Exit 1
    }
    $result | ConvertFrom-Json
}
```

1. The function accepts a parameter `$Command`, this is the Azure CLI command (not
including `az`);
1. The full Azure CLI command is then constructed and printed to
[stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_output_(stdout));
1. The same printed command is then executed using PowerShell's in-build
   `Invoke-Expression` cmdlet;
1. As `Invoke-Expression` may hide errors. The reserved PowerShell variable
   `$LastExitCode` is used to ensure that execution is terminated if the command
   within `Invoke-Expression` is printed to [stderr](https://en.wikipedia.org/wiki/Standard_streams#Standard_error_(stderr)); and finally
1. As the output from the Azure CLI is JSON, it is converted into a PowerShell
   object using the cmdlet `ConvertFrom-Json`.

As this function is within a module, it can be imported easily. To demonstrate,
on the `Invoke-AzureCommands.ps1` file this module is imported as follows:

```PowerShell
Import-Module ./AzCli -Force
```

Once it is imported, the function can then be used. If the command `az group
list` is needed to be called the usage would be as follows:

```PowerShell
Invoke-AzCli -Command "group list"
```

As the above function `Invoke-AzCli` returns a PowerShell object, it can be
piped to common functions like `Measure-Object` or `Where-Object`.

## Conclusion

The Azure CLI is a succinct and powerful automation tool for Azure deployments
that should be considered when using PowerShell scripts to orchestrate
deployments. It is easily integrated into PowerShell scripts if the running
machine has the Azure CLI installed by using the `Invoke-Expression` cmdlet. With
the proper architecture in-place, the Azure CLI can be integrated into a
PowerShell script with logging and debugging. As the Azure CLI returns JSON
objects, PowerShell scripts can parse and process the data returned from the
Azure CLI for further automation or output to the machine running the script.
