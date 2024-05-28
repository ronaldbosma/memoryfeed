# Application Insights

You can use the following PowerShell script to send a CSV file to Application Insights. This can be useful when troubleshooting or monitoring your application.

```powershell
$instrumentationKey = "..."
$endpoint = "https://.....in.applicationinsights.azure.com/v2/track"

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Content-Type", "application/json")

$body = @"
{
    "name": "MetricData",
    "time": "$([System.dateTime]::UtcNow.ToString('o'))",
    "iKey": "$instrumentationKey",
    "data": {
        "baseType": "EventData",
        "baseData": {
            "ver": 2,
            "name": "Custom event",
            "properties": {
                "customProperty": "customValue"
            }
        }
    }
}
"@
 
$response = Invoke-RestMethod $endpoint -Method 'POST' -Headers $headers -Body $body
$response | ConvertTo-Json
```