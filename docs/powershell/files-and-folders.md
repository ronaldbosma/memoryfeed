# Files and Folders

## Remove all folders with specific name

```powershell
Get-ChildItem -Include "<folder-name>" -Directory -Recurse | Remove-Item -Force -Recurse -Confirm:$false
```

## Replace files that have specific name

Finds files with the name `the-file.jpg` in the current folder & subfolders and replaces it with `C:\Temp\new.jpg`.

```powershell
Get-ChildItem -Include "the-file.jpg" -Recurse | %{ Copy-Item -Path "C:\Temp\new.jpg" -Destination $_.FullName -Force }
```

If the source file is in the current folder or a subfolder, you'll get an error that it can't overwrite itself. To prevent this, use the following script. Note that it uses the file name of the source file as the filter to select the files to replace.

```powershell
$sourceFilePath = "C:\Temp\new.jpg"
$targetFileName = (Get-Item -Path $sourceFilePath).Name
Get-ChildItem -Include $targetFileName -Recurse | Where-Object { $_.FullName -ne $sourceFilePath } | %{ Copy-Item -Path $sourceFilePath -Destination $_.FullName -Force }
```

## Copy files matching filter (include source folder structure)

This script selects all files in the source folder and subfolders matching the specified filter. 
It will copy the files to the target folder, recreating the subfolder structur if applicable.

```powershell
$sourceFolder = "C:\temp\source"
$targetRootFolder = "C:\temp\target"
$fileFilter = "*.local.*"

$selectedFiles = Get-ChildItem -Path $sourceFolder -Filter $fileFilter -Recurse -File

foreach ($selectedFile in $selectedFiles) {
    $targetFolder = $selectedFile.Directory.FullName.Replace($sourceFolder, $targetRootFolder)
    $targetFile = $selectedFile.FullName.Replace($sourceFolder, $targetRootFolder)

    if (-not(Test-Path -Path $targetFolder)) {
        Write-Host "Create $targetFolder"
        $tmp = New-Item -Path $targetFolder -ItemType "directory" -Force
    }

    Write-Host "Copy $($selectedFile.FullName) to $targetFile"
    Copy-Item -Path $selectedFile.FullName -Destination $targetFile -Recurse -Force
}
```

## Update multiple files

This function can be used to update multiple files that match the filter criteria. It can:
- add new content at the top or bottom of the file
- add new content before or after a specific line
- replace old content with new content

```powershell
<#
    .SYNOPSIS
        Updates all files matching filter criteria with new content.

    .DESCRIPTION
        Updates all files that match the filter criteria with the specified new content.
        Uses Get-ChildItem to select files.

        With the -AtTop, -AtBottom, -BeforeLine and -AfterLine parameters you can specify where to add new content.
        These parameters can be combined to add the same new content in different places.

    .EXAMPLE
        Add the 'using System;' line to the top of every Program.cs.
        PS C:\> Update-FileContent -Path "C:\repos\foo" -Filter "Program.cs" -NewContent "using System;" -AtTop

    .EXAMPLE
        Add the 'using System.Linq;' line after the 'using System;' line in every Program.cs.
        PS C:\> Update-FileContent -Path "C:\repos\foo" -Filter "Program.cs" -NewContent "using System.Linq;" -AfterLine "using System;"

    .EXAMPLE
        Replace the text 'Foo' with 'Bar' in every Program.cs.
        PS C:\> Update-FileContent -Path "C:\repos\foo" -Filter "Program.cs" -OldContent "Foo" -NewContent "Bar"
#>
function Update-FileContent
{
    [CmdletBinding()]
    param(
        # Specifies a path to one or more locations. Wildcards are accepted. The default location is the current directory (.).
        [Parameter(Mandatory=$true)]
        [string[]]$Path,

        # Specifies a filter to qualify the Path parameter. The * and ? wildcards are supported.
        [Parameter(Mandatory=$true)]
        [string]$Filter,

        # Specifies an array of one or more string patterns to be matched as the cmdlet gets child items.
        # Any matching item is included in the output. Enter a path element or pattern, such as "*.txt".
        # Wildcard characters are permitted.
        # The Include parameter is effective only when the command includes the contents of an item, such as C:\Windows\*,
        # where the wildcard character specifies the contents of the C:\Windows directory.
        [Parameter()]
        [string[]]$Include,

        # Specifies an array of one or more string patterns to be matched as the cmdlet gets child items.
        # Any matching item is excluded from the output.
        # Enter a path element or pattern, such as *.txt or A*. Wildcard characters are accepted.
        [Parameter()]
        [string[]]$Exclude,

        # If specified, files in a folder with this name are excluded.
        [Parameter()]
        [string]$ExcludeFilesInFolder = $null,

        # The new content to add to the files. The @"..."@ syntax can be used to add multi-line content.
        [Parameter(Mandatory=$true)]
        [string]$NewContent,

        # When true, new content is added to the top of the files.
        [Parameter()]
        [switch]$AtTop,

        # When true, new content is added to the bottom of the files.
        [Parameter()]
        [switch]$AtBottom,

        # When specified, new content is added before lines that exactly match this value.
        [Parameter()]
        [string]$BeforeLine = $null,

        # When specified, new content is added after lines that exactly match this value.
        [Parameter()]
        [string]$AfterLine = $null,

        # When specified, the old content is replace by the new content. 
        # - The @"..."@ syntax can be used to specify multi-line content.
        # - The @'...'@ syntax can be used to specify multi-line content that has characters like ".
        [Parameter()]
        [string]$OldContent = $null
    )

    $files = Get-ChildItem -Path $Path -Filter $Filter -Include $Include -Exclude $Exclude -File -Recurse

    foreach ($file in $files)
    {
        # Skip file if it's in a folder that should be excluded
        if (-not([string]::IsNullOrWhiteSpace($ExcludeFilesInFolder)) -and $file.Directory.Name -eq $ExcludeFilesInFolder) {
            continue
        }

        Write-Host "Update $file"

        $content = Get-Content -Path $file.FullName
        $lines = New-Object System.Collections.ArrayList(,$content)

        # Add new content at the top of the file
        if ($AtTop) {
            $lines.Insert(0, $newContent)
        }

        # Add new content at the bottom of the file
        if ($AtBottom) {
            $lines.Add($newContent) | Out-Null
        }

        # New content should be added before and/or after a specific line
        if (-not([string]::IsNullOrEmpty($BeforeLine)) -or -not([string]::IsNullOrEmpty($AfterLine)))
        {
            $offset = 1;
            # Copy the lines array list so we can make changes while iterating
            $newLines = New-Object System.Collections.ArrayList(,$lines)

            for ($i = 0; $i -lt $lines.Count; $i++)
            {
                # Add new content before the current line
                if (-not([string]::IsNullOrEmpty($BeforeLine)) -and $lines[$i] -eq $BeforeLine) {
                    $newLines.Insert($i+$offset-1, $NewContent)
                    #Increase offset so new lines are inserted in the correct position when there are multiple matches
                    $offset++
                }

                # Add new content after the current line
                if (-not([string]::IsNullOrEmpty($AfterLine)) -and $lines[$i] -eq $AfterLine) {
                    $newLines.Insert($i+$offset, $NewContent)
                    #Increase offset so new lines are inserted in the correct position when there are multiple matches
                    $offset++
                }
            }

            $lines = $newLines
        }

        Set-Content -Path $file.FullName -Value $lines

        # Old content should be replaced by new content
        if (-not([string]::IsNullOrEmpty($OldContent)))
        {
            # Reload the (already changed) file content. The -Raw parameter is necessary for the regex replace to work correctly.
            $rawContent = Get-Content -Path $file.FullName -Raw

            # Escape the old content to make sure it's treated as a literal string
            $escapedOldContent = [regex]::Escape($OldContent)
            # Replace the old content with the new content
            $result = $rawContent -replace $escapedOldContent, $NewContent

            Set-Content -Path $file.FullName -Value $result
        }
    }
}
```