# Pipeline for NuGet package

This Azure DevOps build pipeline will build a .NET Core/Standard project and publish the generated nupkg file as an artifact.

Make sure you add all the necessary settings in the csproj and also add `<GeneratePackageOnBuild>true</GeneratePackageOnBuild>` to generate the NuGet package during the build.

In the build step the output folder is `$(Build.ArtifactStagingDirectory)`. This will make sure no other nupkg files (e.g. from dependencies) are in the result.

If you generate the nupkg in another folder and want to copy it you might also copy the folder structure. Use the `flattenFolders` setting to prevent this.

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
- task: DotNetCoreInstaller@0
  inputs:
    version: '2.2.x'

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
    arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'

- task: CopyFiles@2
  displayName: 'Copy NuGet Package'
  inputs:
    SourceFolder: '$(Build.ArtifactStagingDirectory)'
    Contents: '*.nupkg'
    TargetFolder: '$(Build.ArtifactStagingDirectory)/Package'

- task: PublishBuildArtifacts@1
  displayName: 'Publish Artifact'
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)/Package'
```