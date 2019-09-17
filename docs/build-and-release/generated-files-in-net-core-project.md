# Generated files in .NET Core project

If you generate files in a .NET Core (SDK) project, like service agents, the first build of the project will fail because it didn't find the generated code files. You need to explicitly specify the generated code files in the project as shown in the following example.

```XML
<Project Sdk="Microsoft.NET.Sdk">

  <Target Name="GenerateServiceAgents" BeforeTargets="PreBuildEvent">
    <Exec Command="..." />
  </Target>

  <!-- 
    dotnet does a lookup of the files that should be included in the project, before the targets like 'GenerateServiceAgents' are executed.
    Om ervoor te zorgen dat de agents gecompileerd worden moeten we ze expliciet definieren.
  -->
  <ItemGroup>
    <Compile Include="./Generated/agent01.cs" />
    <Compile Include="./Generated/agent02.cs" />
  </ItemGroup>
</Project>
```


Comment from https://github.com/dotnet/sdk/issues/1593:

Globs (*.cs) are calculated during evaluation time, in particular during lazy item evaluation. The phases in a build look like this:

```
1. Evaluation
1.1 Properties
1.2 ItemDefinitionGroup
1.3 Items
1.3.1 Lazy Items
1.4 UsingTasks (conditions)
1.5 Targets (conditions)

2. Run Targets
```