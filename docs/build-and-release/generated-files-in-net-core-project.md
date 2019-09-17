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