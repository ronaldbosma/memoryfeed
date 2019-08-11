# Exclude project from code coverage in SonarQube

To exclude a project from code coverage in SonarQube add the following XML to the *.csproj.

```XML
  <ItemGroup>
    <SonarQubeSetting Include="sonar.coverage.exclusions">
      <Value>**</Value>
    </SonarQubeSetting>
  </ItemGroup>
```

Note that excluding projects in a runsettings file doesn't work. The project are excluded when measuring code coverage. However SonarQube will then report 0% code coverage.