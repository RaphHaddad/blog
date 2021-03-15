---
title: 'Python to Automate Azure Deployments'
date: 2021-03-16T00:00:00.001+11:00
draft: false
tags : [DevOps, Azure, Azure DevOps, Cloud]
---

During a review of a previous blog post, my colleague [Paul Glavich](https://weblogs.asp.net/pglavich) asked me to
justify the technology choice of Python as a scripting language to automate
Azure deployments. I told him that justifying the decision in the current post
may render the justification insufficient, as such, I promised him that I will
attempt to justify this decision in a subsequent blog post (the details of our
dialogue is on this [GitHub pull
request](https://github.com/RaphHaddad/blog/pull/22)).
This post is a fulfilment of that promise.

## Context

This blog post will examine the efficacy of Python as a technology choice for
automating Azure deployments. Any technology choice should take into
consideration
many factors. These factors include (amongst many):

- Usage of the technology in the organisation;
- Uptake of the technology in the wider community;
- Risk tolerance of the organisation for newer, potentially volatile technology; and
- The availability of the skillset in the market.

## Linux (Ubuntu) Build Agents

The first reason to use Python is the usage of Linux build agents on Azure DevOps. Linux build agents have a substantially smaller compute and memory footprint than Windows agents and are far more interoperable with other build and release management tools (Linux is by far the most deployed operating system on web servers).

Python does work on Window machines, however, it is not included in Windows builds. On the other hand, Python is included in most Linux distributions and if it is not, can easily be installed via package managers that come with Linux distributions.

In addition, Python libraries like subprocess and argparse work natively with Linux without providing additional flags or taking into consideration different shells (like PowerShell or Command Prompt).

![Microsoft Loves Linux Logo](/images/ms-heart-linux.png "Microsoft Loves Linux Logo")

## The Usage of the Azure CLI

The Azure CLI provides a simple way of interacting with Azure via any command line (Windows, macOS, or Linux) and whilst the Azure CLI works great within Windows, the Azure CLI is idiomatically Unix-oriented in that the CLI and the sub-commands associated with it do ["one thing and do it well"](https://en.m.wikipedia.org/wiki/Unix_philosophy). This means that chaining commands is a lot easier than the PowerShell cmdlets.

The Azure CLI can definitely be run on Windows, however, due to the different shells available on Windows (PowerShell, Command Prompt) a script may experience negative unintended consequences. Specifically, Windows shells such as PowerShell and Command Prompt deal with quotes (and escaping them) differently. [See this documentation from Microsoft](https://docs.microsoft.com/en-us/cli/azure/use-cli-effectively#quoting-issues) that goes into this issue in more detail.

Python’s design makes it easy to split code in modules, files, and classes. This helps keep related code close to each other and aides in dependancy management of modules. Using the Azure CLI within Python is simple by either creating a wrapper using the Python libraries argparse and json, or using the [existing pip library by Microsoft](https://github.com/Azure/azure-cli). In addition, the Azure CLI itself is written in Python, so if a bug is found, context switching into the Azure CLI code won’t be a strenuous affair.  

## Integration with Infrastructure Template Technologies

Due to the ease of modularisation explained above, Python also makes it easy to integrate with infrastructure template technologies such as Terraform, Bicep, or ARM Templates.  Python’s libraries that integrate with the host operating system’s shell means that CLI tools from a variety of providers can be used by building out the necessary wrappers and modules over them. 

## Readability and Conciseness

Many scripting languages run on Linux: Perl, Ruby, and Bash (amongst many). As a consequence, choosing Python over other languages may seem inconsequential, however in my opinion, Python is a lot more readable and easier to learn than many other languages. Many introduction to coding courses are now using Python to teach coding (both in schools and in higher education institutes). A testament to the simplicity of the language. 

## Conclusion

Python is a great language to accelerate the creation of Azure automation scripts due to its maturity and uptake within the wider automation ecosystem (and specifically the Microsoft ecosystem). It is concise, supported,  interoperable, and runs on a variety of build agents. It saying this, a technological choice should always take into consideration several factors that encompass the organisation to which it will be introduced.

Once again, thank you for reading this post. I hope you got something out of it. 
