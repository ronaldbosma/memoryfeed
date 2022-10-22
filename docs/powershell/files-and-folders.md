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