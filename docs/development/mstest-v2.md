# MSTest V2


## Use enum values as test data source

Add the `EnumTestDataSource` attribute to the test method, and pass the enum type into the constructor.  
Add the enum as parameter to the test method.

```csharp
[DataTestMethod]
[EnumTestDataSource(typeof(MyEnum))]
public void TestMethod(MyEnum myEnum)
{
}
```

The `EnumTestDataSourceAttribute` looks like this.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;
using System.Reflection;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace Example
{
    public sealed class EnumTestDataSourceAttribute : Attribute, ITestDataSource
    {
        private readonly Type _enumDataSourceType;

        public EnumTestDataSourceAttribute(Type enumDataSourceType)
        {
            _enumDataSourceType = enumDataSourceType;
        }

        public IEnumerable<object[]> GetData(MethodInfo methodInfo)
        {
            return _enumDataSourceType.GetEnumValues()
                .Cast<object>()
                .Select(e => new[] { e });
        }

        public string? GetDisplayName(MethodInfo methodInfo, object[] data)
        {
            return data is not null
                ? string.Format(CultureInfo.CurrentCulture, "Custom - {0} ({1})", methodInfo.Name, string.Join(",", data))
                : null;
        }
    }
}

```