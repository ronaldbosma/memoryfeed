# SonarQube

## Host SonarQube in Azure

How to use SonarQube in Azure DevOps using an Azure Container instance to host SonarQube: https://www.azuredevopslabs.com/labs/vstsextend/sonarqube/.

- Start Cloud Shell in Azure Portal with Bash.
- Create Resource Group:
  `az group create --name sonarqube-rg --location westeurope`
- Create Azure Container Instance based on SonarQube Docker image:
  `az container create -g sonarqube-rg --name ronaldbosma-sonarqube-aci --image sonarqube --ports 9000 --dns-name-label ronaldbosma-sonarqube --cpu 2 --memory 3.5`
- SonarQube will be reachable on: http://ronaldbosma-sonarqube.westeurope.azurecontainer.io:9000

## Host SonarQube in local container

### Setup
> Command below installs SonarQube Community. Which can't analyse SQL. You'll need the Developer edition for that.

https://docs.sonarqube.org/latest/setup/get-started-2-minutes/

1. Get and run container
```
docker run -d --name sonarqube -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:latest
```
1. Navigate to http://localhost:9000/
1. Login with username `admin` and password `admin`.
1. Change password

Generate token:
1. Create new project manually/locally
1. Choose Analyse project locally
1. Generate token

### Perform an analysis
Install sonar scanner from https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/

Save the following PowerShell code in a script called `perform-analysis.ps1` and make the necessary changes:
- Change the path to `SonarScanner.MSBuild.exe`.
- Replace `<token>` with your token.
- Change the path to `MSBuild.exe` or remove it and enable the `dotnet` command.

To perform an analysis, execute the script, passing in the path to a solution file. E.g. `.\perform-analysis.ps1 C:\temp\solution.sln`

```powershell
param($solutionFilePath)

$solutionFileName = Split-Path $solutionFilePath -Leaf
$solutionFolder = Split-Path $solutionFilePath
$solutionName = [io.path]::GetFileNameWithoutExtension($solutionFilePath)

Push-Location $solutionFolder

Write-Host "Solution: $solutionFileName"
Write-Host "Project: $solutionName"
Write-Host "Location: $(Get-Location)"

Write-Host "NuGet Restore"
nuget.exe restore

Write-Host "Start analysis"
C:\Temp\sonar-scanner-msbuild-5.4.1.41282-net46\SonarScanner.MSBuild.exe begin /k:$solutionName /d:sonar.login="<token>"

Write-Host "Build solution"
& "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Current\Bin\MSBuild.exe" $solutionFileName /t:Rebuild
#dotnet build $solutionFileName

Write-Host "Finish analysis"
C:\Temp\sonar-scanner-msbuild-5.4.1.41282-net46\SonarScanner.MSBuild.exe end /d:sonar.login="<token>"

Pop-Location
```