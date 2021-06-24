# Set Tab Title

You can use one of the following PowerShell functions to set the tab title of a Windows Terminal tab.

```powershell
function Set-TabTitleFromCurrentFolderName()
{
    $currentFolderName = Split-Path -leaf -path (Get-Location)
    Set-TabTitle $currentFolderName
}

function Set-TabTitle([string]$title)
{
    $Host.UI.RawUI.WindowTitle = $title
}
```