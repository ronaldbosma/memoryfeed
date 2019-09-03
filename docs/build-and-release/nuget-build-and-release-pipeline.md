# Pipeline for NuGet package

This Azure DevOps build pipeline will build a .NET Core/Standard project and create two NuGet packages. A prerelease and release version. The packages are published as an artifact of the build.

## Project file
If you have a `Version` element in your csproj then change in into `VersionPrefix`. This will allow for the build pipeline to add a suffix for the prerelease package. (`VersionSuffix` can be left empty.)

## Build pipeline

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

steps:
- task: UseDotNet@2
  displayName: 'Use .NET Core sdk'
  inputs:
    packageType: sdk
    version: 2.2.x
    installationPath: $(Agent.ToolsDirectory)/dotnet

- task: DotNetCoreCLI@2
  displayName: "NuGet Restore"
  inputs:
    command: restore
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  displayName: "Build Solution"
  inputs:
    command: build
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

- task: DotNetCoreCLI@2
  displayName: 'Create NuGet Package - Prerelease Version'
  inputs:
    command: pack
    buildProperties: 'VersionSuffix="$(Build.SourceVersion)"'
    packDirectory: '$(Build.ArtifactStagingDirectory)/packages/prereleases'
    arguments: '--configuration $(buildConfiguration)'

- task: DotNetCoreCLI@2
  displayName: 'Create NuGet Package - Release Version'
  inputs:
    command: pack
    packDirectory: '$(Build.ArtifactStagingDirectory)/packages/releases'
    arguments: '--configuration $(buildConfiguration)'
    nobuild: true

- task: PublishBuildArtifacts@1
  displayName: 'Publish Artifact'
  inputs:
    artifactName: 'FluentAssertions.ArgumentMatchers.Moq'
    PathtoPublish: '$(Build.ArtifactStagingDirectory)/packages'

```