---
tags: [ "Azure DevOps CLI", "Azure DevOps", "Azure CLI" ]
---

# Azure DevOps CLI

Download Azure CLI from https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest and install it.

Commands can be run from e.g. a Developer Prompt.

## Commands

### Setup

#### Install Azure DevOps extension
`az extension add --name azure-devops`

#### List installed extensions
`az extension list`

#### Login
`az login`

#### Configure defaults (not required)
`az devops configure --defaults organization=https://dev.azure.com/ronaldbosma project=GitHub`

### Pipelines

#### List pipelines
`az pipelines list`

#### Show details of pipeline
`az pipelines show --name ronaldbosma.FluentAssertions.ArgumentMatchers.Moq`

#### Select pipeline ids
`az pipelines show --name ronaldbosma.FluentAssertions.ArgumentMatchers.Moq | ConvertFrom-JSON | Select id`

#### List buids (instances) from pipeline
`az pipelines build list --definition-ids 6`

#### List in progress builds for specific pipeline
`az pipelines build list --definition-ids 6 --status inProgress`

#### List build numbers of in progress builds for specific pipeline
`az pipelines build list --definition-ids 6 --status inProgress | ConvertFrom-JSON | % { $_.buildNumber }`  
or  
`az pipelines runs list --pipeline-ids 6 --status inProgress | ConvertFrom-JSON | % { $_.buildNumber }`
