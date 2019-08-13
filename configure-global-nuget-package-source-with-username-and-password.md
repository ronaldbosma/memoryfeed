---
tags: [ "NuGet" ]
---

# # Configure global NuGet package source with username and password

You can register a global NuGet package source by adding it to a common NuGet.config. E.g. %appdata%\NuGet\NuGet.Config.
See https://docs.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior for more info.

You can use the following command to configure a username and password for a package source:

```
nuget sources Update -Name <source-name> -Username <username> -Password <password>
```