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