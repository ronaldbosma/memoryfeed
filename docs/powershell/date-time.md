# Date & Time

## Convert Unix Timestamp

Times in for example a JWT token are in Unix Timestamp format. You can use the following to convert them to a readable date time.

```powershell
$unixTimeStamp = 1684138861

$date1=(Get-Date 01.01.1970)+([System.TimeSpan]::fromseconds($unixTimeStamp))
$date1.ToString("yyyy-MM-dd HH:mm:ss.ffffff")

$date2 = (Get-Date 01.01.1970).AddSeconds($unixTimeStamp)
($date2).ToString("yyyy-MM-dd HH:mm:ss.ffffff")
```