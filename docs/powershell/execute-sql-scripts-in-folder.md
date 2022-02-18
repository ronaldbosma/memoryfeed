# Execute SQL scripts in folder

The following PowerShell script can be used to execute all SQL scripts in a folder.

```powershell
$localScriptRoot = "C:\Temp\Scripts"
$server = "(localdb)\MSSQLLocalDB"
$database = "MyDatabase"

# If Import-Module fails, use `Install-Module "SqlServer"` to install the SqlServer module for the first time
if (!(Get-Module -Name "SqlServer"))
{
	Write-Host 'Loading SqlServer Module' -ForegroundColor DarkYellow
	Push-Location
	Import-Module "SqlServer" -DisableNameChecking
	Pop-Location
}

$scripts = Get-ChildItem $localScriptRoot | Where-Object {$_.Extension -eq ".sql"}

Write-Host "Execute $($scripts.length) SQL scripts on server $server and database $database from folder: $localScriptRoot"
$execute = Read-Host -Prompt "[yes/no]"
if (($execute.tolower() -eq "yes") -or ($execute.tolower() -eq "y"))
{
	foreach ($script in $scripts)
	{
		Write-Host "Execute: $($script.FullName)"
		Invoke-Sqlcmd -Server $server -Database $database -InputFile $script.FullName
	}
	
}
else
{
	Write-Host "Execution cancelled"
}
```