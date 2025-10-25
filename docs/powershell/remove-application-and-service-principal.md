# Remove an application and service principal from Entra ID

This PowerShell function removes an application and its associated service principal from Entra ID based on a provided unique name. 
It first checks if the application exists, then deletes both the service principal and the application, ensuring they are permanently removed. 
To use this function, call it with the unique name of the application you want to remove: `Remove-ApplicationAndServicePrincipal <uniqueName>`

```powershell
function Remove-ApplicationAndServicePrincipal($uniqueName){
    if ([string]::IsNullOrWhiteSpace($uniqueName)) {
        Write-Host "No unique name provided. Skipping deletion of application and service principal."
        return
    }

    # Get the application with the unique name
    $app = az ad app list | ConvertFrom-Json | Where-Object { $_.uniqueName -eq $uniqueName }

    if ($app) {
        # Get the service principal of the application
        $sp = az ad sp list --all | ConvertFrom-Json | Where-Object { $_.appId -eq $app.appId }
        
        if ($sp) {
            Write-Host "Deleting service principal $($sp.id) of application with unique name $uniqueName"
            # Delete the service principal (moves the service principal to the deleted items)
            az ad sp delete --id $sp.id
            # Permanently delete the service principal. If we don't do this, we can't create a new service principal with the same name.
            az rest --method DELETE --url "https://graph.microsoft.com/beta/directory/deleteditems/$($sp.id)"
        }
        else {
            Write-Host "Unable to delete service princpal for application with unique name $uniqueName. Service principal not found."
        }

        Write-Host "Deleting application $($app.id) with unique name $uniqueName"
        # Delete the application (moves the application to the deleted items)
        az ad app delete --id $app.id
        # Permanently delete the application. If we don't do this, we can't create a new application with the same name.
        az rest --method DELETE --url "https://graph.microsoft.com/beta/directory/deleteditems/$($app.id)"

    } else {
        Write-Host "Unable to delete application with unique name $uniqueName. Application not found."
    }
}
```