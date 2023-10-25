# Base64 Conversion

PowerShell functions to convert text to and from base64

```PowerShell
function ConvertTo-Base64($text)
{
    $encodedBytes = [System.Text.Encoding]::UTF8.GetBytes($text)
    $encodedText = [System.Convert]::ToBase64String($encodedBytes)
    return $encodedText
}

function ConvertFrom-Base64($encodedText)
{
    $decodedBytes = [System.Convert]::FromBase64String($encodedText)
    $decodedText = [System.Text.Encoding]::Utf8.GetString($decodedBytes)
    return $decodedText
}
```

## Convert base64 to file

The sample below loads the `base64-input.txt` file containing a base64 string and save it as a `.pdf` file.

```powershell
$base64 = Get-Content ./base64-input.txt
$bytes = [Convert]::FromBase64String($base64)
# WriteAllBytes requires absolute path, will fail silently otherwise
[IO.File]::WriteAllBytes("$pwd/$(Get-Date -Format "yyyyMMdd-HHmmss").pdf", $bytes)
```