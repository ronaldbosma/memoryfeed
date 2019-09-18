# Create custom Azure DevOps task resources

## Create a build or release task
https://docs.microsoft.com/en-us/azure/devops/extend/develop/add-build-task?view=azure-devops

## Blog: Creating a Custom Build Task in VSTS (Visual Studio Team Services)
Example of how to create a custom task that uses a PowerShell script

https://www.andrewhoefling.com/Blog/Post/dev-ops-vsts-custom-build-task-extension

## Logging
https://github.com/microsoft/azure-pipelines-tasks/blob/master/docs/authoring/commands.md
http://hauge.cloud/2018/06/22/improving-logs-in-visual-studio-team-services-vsts-with-special-tags/ 

## Blog: Building Azure DevOps Extension on Azure DevOps – Automated Publish 1
https://devkimchi.com/2019/07/24/building-azure-devops-extension-on-azure-devops-5/
https://devkimchi.com/2019/07/31/building-azure-devops-extension-on-azure-devops-6/

## Commands

### Install CLI tool:
```
npm i -g tfx-cli
```

### Package extension:
```
tfx extension create --manifest-globs vss-extension.json
```

### Publish extension:
Replace `<extension-name>` with the name of your extension. Replace `<token>` with a Personal Access Token that can publish to the Marketplace. Replace `<organization>` with the organization you want to share the extension with.

```
tfx extension publish --json --no-color --service-url https://marketplace.visualstudio.com/ --auth-type pat --token <token> --vsix <extension-name>.vsix --share-with <organization> --debug-log-stream stderr
```

## Create Personal Access Token to publish

In Azure DevOps:
- Click on your avatar.
- Choose _Security_.
- Click _New Token_.
- Select _All accessible organizations_ as _Organization_. (Selecting a specific organization will result in a 401 when publishing the extension.)
- Click _Show all scopes_.
- Select _Publish_ under _Marketplace_.