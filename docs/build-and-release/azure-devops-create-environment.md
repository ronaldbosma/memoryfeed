# Azure DevOps - Create environment

Here's a script to create an Azure DevOps environment, based on https://www.colinsalmcorner.com/az-devops-like-a-boss/.

>NOTE: if you specify and environment name in a YAML pipeline, Azure DevOps will create the environment for you.

You'd expect the token to need the 'Environment (Read & manage)' scope, but it needs 'Tokens (Read & manage)'.

```powershell
$OrganizationUrl = "https://myaccount.visualstudio.com"
$TeamProject = "myProject"
$Environment = "myEnvironment"
$Token = "xyz"


Write-Host "Log in to Azure DevOps organization $OrganizationUrl"
"$Token" | az devops login --organization $OrganizationUrl


Write-Host "Check if environment $Environment exists in Azure DevOps team project $TeamProject"
$environmentId = az devops invoke `
    --area distributedtask `
    --resource environments `
    --route-parameters project=$TeamProject `
    --org $OrganizationUrl `
    --api-version "6.0-preview" `
    --query "value[?name=='$Environment'].id" `
    --output tsv


if ($null -eq $environmentId)
{
    Write-Host "Create environment $Environment in Azure DevOps team project $TeamProject"

    $createEnvironmentBody = @{ name = $Environment; description = "Provisioned environment $Environment" };
    $createEnvironmentFile = "create-environment-body.json";
    Set-Content -Path $createEnvironmentFile -Value ($createEnvironmentBody | ConvertTo-Json);

    $environment = az devops invoke `
        --area distributedtask `
        --resource environments `
        --route-parameters project=$TeamProject `
        --org $OrganizationUrl `
        --http-method POST `
        --in-file $createEnvironmentFile `
        --api-version "6.0-preview";

    Remove-Item $createEnvironmentFile -Force;
}
else
{
    Write-Host "Environment $Environment already exists in Azure DevOps team project $TeamProject"
}


Write-Host "Log out of Azure DevOps organization $OrganizationUrl"
az devops logout
```