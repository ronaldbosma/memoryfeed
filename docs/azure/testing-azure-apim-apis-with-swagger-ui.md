# Testing Azure API Management APIs with Swagger UI

Some things to consider when testing your APIs hosted on Azure API Management with a tool like Swagger UI.

## Cross-origin

By default, trying out an API hosted API Management from Swagger UI (VS Code) doesn't work.
The call is being made from a browser, which is handled as a cross origin call.
If you look in the logging, an `OPTIONS` call is performed on the endpoint that you're trying out which will most likely return a 404 if you haven't implemented/exposed this endpoint.

Cross origin can be enabled to make this work.
See https://learn.microsoft.com/en-us/azure/api-management/enable-cors-developer-portal for documentation on how to do this in relation to the APIM developer portal.


## OAuth security scheme

It's possible to configure an oauth2 security scheme in your OpenAPI spec so you can retrieve an access token in e.g. Swagger UI to try out an API.
See the following example for a client credential flow with Entra ID.

```json
"securitySchemes": {
	"oauth": {
		"type": "oauth2",
		"flows": {
			"clientCredentials": {
				"tokenUrl": "https://login.microsoftonline.com/<your-tenant-id>/oauth2/v2.0/token",
				"scopes": {
					"<your-scope>": "Sample"
				}
			}
		}
	}
}
```

If you have an app registration that's used for server to server communication, retrieving the token will most likely fail. 
You might see a failure in the sign-in logs of the client app registrations service principal (enterprise application) with the following details:
- Failure reason:  
  Cross-origin token redemption is permitted only for the 'Single-Page Application' client-type. Request origin: '{origin}'.
- Additional details:  
  The application must fix either the reply URIs registered on the application registration to include a unique reply address of type "spa", or they must fix the token request to not include an Origin header, if being sent from a non-browser client.

Because the call is made from a browser, it's recognized as a cross-origin call. 
If necessary, you can configure the app registration to allow such calls.
See https://stackoverflow.com/questions/78200733/aadsts9002326-cross-origin-token-redemption-is-permitted-only-for-the-single-p for an example.

As an alternative, you could configure a bearer security scheme so the user can configure the bearer JWT token. 
See the snippet below. 
The user will has to retrieve the access token another way. E.g. via Postman or an HTTP file.

```json
"components": {
	"securitySchemes": {
		"bearerAuth": {
			"type": "http",
			"scheme": "bearer",
			"bearerFormat": "JWT"
		}
	}
}
```
