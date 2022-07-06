# Files and Folders

## Remove all folders with specific name

```
Get-ChildItem -Include "<folder-name>" -Directory -Recurse | Remove-Item -Force -Recurse -Confirm:$false
```