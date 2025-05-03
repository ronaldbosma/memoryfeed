# Permanently remove soft-deleted Log Analytics Workspaces

Here's a script to list all soft-deleted Log Analytics workspaces in your Azure subscription and permanently delete them. 
It will ask for confirmation before proceeding with the deletion.

Permanently deleting a workspace is only possible if the workspace is not in the soft-deleted state.
So, this script first recovers the workspace by creating the resource group (if it doesn't exists) and then recovering the workspace.
After permanently deleting the workspace, it will delete the resource group if it was created by this script.

```pwsh
# List all soft-deleted Log Analytics workspaces and output their names
$deletedWorkspaces = az monitor log-analytics workspace list-deleted-workspaces `
    --query "[].{name:name, resourceGroup:resourceGroup, location:location}" `
    --output json | ConvertFrom-Json

# List all found workspaces and ask for confirmation
Write-Output "The following soft-deleted workspaces were found:"
$deletedWorkspaces | Format-Table -AutoSize

$confirmation = Read-Host "Do you want to proceed with PERMANENTLY deleting these workspaces? (yes/no)"
if ($confirmation -ne "yes") {
    Write-Output "Operation canceled by the user."
    exit
}

foreach ($workspace in $deletedWorkspaces) {
    Write-Output "Workspace Name: $($workspace.name)"

    # Check if resource group exists
    $resourceGroupExists = az group exists --name $workspace.resourceGroup

    # If resource group does not exist, create it so we can recover the workspace
    if ($resourceGroupExists -eq "false") {
        Write-Output "Resource group $($workspace.resourceGroup) does not exist. Creating it."
        az group create --name $workspace.resourceGroup --location $workspace.location
    } else {
        Write-Output "Resource group $($workspace.resourceGroup) already exists."
    }

    # Recover the workspace. We can only permanently delete a workspace if it is NOT in the soft-deleted state.
    Write-Output "Recovering workspace $($workspace.name) in resource group $($workspace.resourceGroup)"
    az monitor log-analytics workspace recover --resource-group $workspace.resourceGroup --workspace-name $workspace.name

    # Permanently delete the workspace
    Write-Output "Permanently deleting workspace $($workspace.name) in resource group $($workspace.resourceGroup)"
    az monitor log-analytics workspace delete --resource-group $workspace.resourceGroup --workspace-name $workspace.name --force --yes

    # If resource group did not exist, remove it
    if ($resourceGroupExists -eq "false") {
        Write-Output "Deleting resource group $($workspace.resourceGroup)"
        az group delete --name $workspace.resourceGroup --yes --no-wait
    } else {
        Write-Output "Resource group $($workspace.resourceGroup) already existed. Skipping deletion."
    }

    Write-Output ""
}
```