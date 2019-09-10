# SpecFlow Practices, Tips & Tricks

This page contains practices, tips & tricks on how to automate scenario's using SpecFlow.

## Unit vs Integration vs UI

Scenario's are preferable implemented as unit test or component integration test. These can run in memory on your machine and during the build.

## Tips

### Configuration

- Configure Dutch language in the app.config instead of each feature file.
- Let scenario's fail when a step definition is not implemented.

Example:

```xml
<specFlow>
  <language feature="nl-NL" />
  <runtime missingOrPendingStepsOutcome="Error" />
  <unitTestProvider name="MsTest" />
</specFlow>
```

## Project structure

A solution has a project named Specs where all scenario's are specified.

The following folder structure is used in every solution to make it easy to find your code.
- a Features folder containing all the feature files
- a StepDefinitions folder containing the Steps classes.
  Step definitions should be grouped by entity/business concept. E.g. person, digital signing.
- a Support folder containing all other automation code. E.g. step argument transformations, value retrievers, etc.
  - step argument transformations will be create in StepArgumentTransformations.cs.

Example:

```
Specs
- Features
  - Register Person.feature
  - Sign Document.feature
- StepDefinitions
  - PersonSteps.cs
  - DocumentSigningSteps.cs
- Support
  - TestDataBuilders
    - PersonBuilder.cs
    - DocumentBuilder.cs
  - StepArgumentTransformations.cs
  - ValueRetrievers
    - MoneyValueRetriever.cs
```

## Assist Helpers

Instead of converting a `Table` to an object yourself use the Assist Helper `CreateSet` and `CreateInstance`.
Combine this with step argument transformations to clean-up your step definition code.

See https://github.com/techtalk/SpecFlow/wiki/SpecFlow-Assist-Helpers for more info.

## Step Argument Transformations

Use  [step argument transformations](https://github.com/techtalk/SpecFlow/wiki/Step-Argument-Conversions) to convert strings to other types.

For example. Here's a step with a euro amount that should be converted to a decimal.

```gherkin
Given a mortgage for € 235.000,-
```
In your step set the type of the parameter to decimal.

```csharp
Given(@"a mortgage for (.*)")]
public void GivenAMortgageFor(decimal amount)
{
    var mortgage = A.Mortgage.For(amount);
}
```

Add a step argument transformation in the `StepArgumentTransformation` class to convert the euro amount string to a decimal

```csharp
[StepArgumentTransformation(@"€ (.*)")]
public decimal EurosToDecimal(string euros)
{            
    return Euros.ConvertFromStringToEuros(euros);
} 
```

## Value Retrievers

Step argument transformations don't work when converting a string in the cell of a table.
In the example below. The euro amount won't be converted to a correct decimal.

```gherkin
Given the following loans
    | Type         | Amount        |
    | Mortgage     | € 235.000,-   |
    | Student Loan | €   4.000,-   |
```

Create a [value retriever](https://github.com/techtalk/SpecFlow/wiki/Value-Retriever-(for-Tables)) to convert the value yourself. Register them in a `BeforeTestRun` hook.

_For SpecFlow versions lower than 3.0_ don't forget that SpecFlow has default value retrievers that might take precedence over your custom value retrievers. You'll have to move the default SpecFlow retrievers after your custom value retriever as shown below.

```csharp
[BeforeTestRun]
public static void BeforeTestRun()
{
    TechTalk.SpecFlow.Assist.Service service = TechTalk.SpecFlow.Assist.Service.Instance;
            
    service.RegisterValueRetriever(new EurosValueRetriever());
    service.MoveValueRetrieverToEndOfList<DecimalValueRetriever>();
    service.MoveValueRetrieverToEndOfList<NullableDecimalValueRetriever>();
}

private static void MoveValueRetrieverToEndOfList<TValueRetriever>(this Service service)
    where TValueRetriever : IValueRetriever
{
    IValueRetriever valueRetriever = service.ValueRetrievers.FirstOrDefault(it => it is TValueRetriever);

    if (valueRetriever != null)
    {
        service.UnregisterValueRetriever(valueRetriever);
        service.RegisterValueRetriever(valueRetriever);
    }
}
```

## Sharing state, stubs, etc. using Context Injection

To be able to share steps between feature files it's considered a _bad practice_ to create step definition files for specific feature files. Instead group your steps bases on an entity like Person or a business concept like Digital Signing.

Sharing state is done through [context injection](https://github.com/techtalk/SpecFlow/wiki/Context-Injection). You can also inject mocks and other classes in your step definitions. You do have to register these types with the object container that SpecFlow uses.

You can use a `BeforeScenario` hook to create and register your mocks etc.:

```csharp
[Binding]
class ConfigureObjectContainerHooks
{
	private readonly IObjectContainer _objectContainer;

	public ConfigureObjectContainerHooks(IObjectContainer objectContainer)
	{
		_objectContainer = objectContainer ?? throw new ArgumentNullException(nameof(objectContainer));
	}

	[BeforeScenario(Order = HookOrder.CreateMocksOrder)]
	public void ConfigureObjectContainerBeforeEachScenario()
	{
		var mock = new Mock<IPersonManager>();
		_objectContainer.RegisterInstanceAs(mock);  //register the mock itself
		_objectContainer.RegisterInstanceAs(mock.Object); //register the interface so we can inject it in our services
	}
}
```

## Generate feature file code behinds during build

To keep your code behinds in sync with your feature files it's considered a best practice to generate the code behinds during the build and not check them in. See https://specflow.org/2019/generating-code-behind-files-using-msbuild/ for an explanation.

Take note though:
- The code behind shouldn't be checked in. Add a .gitignore on the solution level to exclude *.feature.cs.
- Upgrade SpecFlow NuGet package to 2.4.1 because of bug: _Prior to SpecFlow 2.4.1, Visual Studio sometimes does not recognize that a feature file has changed._
- For new feature files you can remove the custom tool from the file properties.