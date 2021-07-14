# ASP.NET

## Fake HttpContext

With the following method you can create a fake `HttpContext` with a `Session`. 
Source: https://stackoverflow.com/questions/9624242/setting-httpcontext-current-session-in-a-unit-test.

```csharp
public static HttpContext FakeHttpContext()
{
	var httpRequest = new HttpRequest("", "http://example.com/", "");
	var stringWriter = new StringWriter();
	var httpResponse = new HttpResponse(stringWriter);
	var httpContext = new HttpContext(httpRequest, httpResponse);

	var sessionContainer = new HttpSessionStateContainer("id", new SessionStateItemCollection(),
											new HttpStaticObjectsCollection(), 10, true,
											HttpCookieMode.AutoDetect,
											SessionStateMode.InProc, false);

	httpContext.Items["AspSession"] = typeof(HttpSessionState).GetConstructor(
								BindingFlags.NonPublic | BindingFlags.Instance,
								null, CallingConventions.Standard,
								new[] { typeof(HttpSessionStateContainer) },
								null)
						.Invoke(new object[] { sessionContainer });

	return httpContext;
}
```