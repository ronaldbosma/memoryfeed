# Output to Console and File

If you wat to see the output of e.g. your Console application in the Console **and** write it to a file, use the `Tee-Object` command.

Here's an example:

```powershell
... | Tee-Object -append -file myoutput.log
```