---
tags: [ "Azure Pipelines", "Azure DevOps", "SonarQube", "Code Coverage" ]
---

# Build pipeline with SonarQube analysis and code coverage

An example of a Azure DevOps build pipeline for .NET Core projects. Also works for .NET Standard projects.  
It performs SonarQube analysis and measures code coverage.

For SonarQube to work the projects need to have a `<ProjectGuid>` element in the csproj with a unique id.

Replace `<your-project-key>` with the SonarQube project key you want to use.  
You might also want to change the version of DotNetCoreInstaller depending on you .NET Core version.

```yaml
# ASP.NET Core (.NET Framework)
# Build and test ASP.NET Core projects targeting the full .NET Framework.
# Add steps that publish symbols, save build artifacts, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/dotnet-core

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'
  sonarQubeProject: '<your-project-key>'

steps:
- task: DotNetCoreInstaller@0
  inputs:
    version: '2.2.x'

- task: DotNetCoreCLI@2
  displayName: "NuGet Restore"
  inputs:
    command: restore
    projects: '**/*.csproj'
    
- task: SonarQubePrepare@4
  inputs:
    SonarQube: 'SonarQube'
    scannerMode: 'MSBuild'
    projectKey: '$(sonarQubeProject)'
    projectName: '$(sonarQubeProject)'
    extraProperties: |
      sonar.cs.opencover.reportsPaths="$(Build.SourcesDirectory)/TestResults/Coverage.opencover.xml"

- task: DotNetCoreCLI@2
  displayName: "Build Solution"
  inputs:
    command: build
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'

- task: DotNetCoreCLI@2
  displayName: 'Run Tests'
  inputs:
    command: test
    projects: '**/*[Tt]ests/*.csproj'
    arguments: '--configuration $(buildConfiguration) --logger trx /p:CollectCoverage=true /p:CoverletOutputFormat=opencover /p:CoverletOutput="$(Build.SourcesDirectory)/TestResults/Coverage"'

- task: SonarQubeAnalyze@4
```

I had a problem that the SonarQube token wasn't passed to the server. I fixed this by configuring a SonarQube username and password in the build and added two extra sonar properties in the SonarQubePrepare task.

```yaml
- task: SonarQubePrepare@4
  inputs:
    #...
    extraProperties: |
      #...
      sonar.login=$(SonarQubeUserName)
      sonar.password=$(SonarQubePassword)
```