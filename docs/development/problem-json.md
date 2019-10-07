# Problem JSON

Using Problem JSON to report errors back to a client. [Understanding Problem JSON](https://medium.com/@sandervanbeek/understanding-problem-json-adf68e5cf1f8).

Example:

```
HTTP/1.1 401 Unauthorized
Content-Type: application/problem+json; charset=utf-8
Date: Wed, 07 Aug 2019 10:10:06 GMT
{
    "type": "https://example.com/probs/cant-view-account-details",
    "title": "Not authorized to view account details",
    "status": 401,
    "detail": "Due to privacy concerns you are not allowed to view account details of others. Only users with the role administrator are allowed to do this.",
    "instance": "/account/123456/details"
}
```

A few highlights:
- The HTTP header Content-Type for a Problem response must be `application/problem+json`. This makes Problem easily identified by the consumer.
- `type` is typically an absolute URL that leads to an HTML page containing human-readable documentation regarding the problem.
- When `type` equals `about:blank` then `title` should equal the description of the HTTP status code.