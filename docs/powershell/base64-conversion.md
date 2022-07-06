# Base64 Conversion

PowerShell functions to convert text to and from base64

```PowerShell
function Convert-ToBase64($text)
{
    $encodedBytes = [System.Text.Encoding]::UTF8.GetBytes($text)
    $encodedText = [System.Convert]::ToBase64String($encodedBytes)
    return $encodedText
}

function Convert-FromBase64($encodedText)
{
    $decodedBytes = [System.Convert]::FromBase64String($encodedText)
    $decodedText = [System.Text.Encoding]::Utf8.GetString($decodedBytes)
    return $decodedText
}
```