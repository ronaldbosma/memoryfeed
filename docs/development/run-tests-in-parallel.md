# Run tests in parallel

Run tests in parallel using MSTest 2. Based on https://github.com/Microsoft/testfx-docs/blob/master/RFCs/004-In-Assembly-Parallel-Execution.md.

## Run settings

Configure a `.runsettings` file with the following content.

```XML
<RunSettings>
<!-- MSTest adapter -->  
  <MSTest>
    <Parallelize>
      <Workers>0</Workers>
      <Scope>ClassLevel</Scope>
    </Parallelize>
  </MSTest>
</RunSettings>
```

## Assembly attribute

```charp
[assembly: Parallelize(Workers = 0, Scope = ExecutionScope.ClassLevel)]
```

## Exclude tests from parallelization

Use the `[DoNotParallelize]` attribute to specify when specific tests should not be parallelized.
