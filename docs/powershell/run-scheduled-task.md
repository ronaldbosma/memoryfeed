# Run Scheduled Task

To get the state of a scheduled task use `Get-ScheduledTask "Task Name"`.  
To start a scheduled task use `Get-ScheduledTask "Task Name" | Start-ScheduledTask`.

The `Start-ScheduledTask` command will not wait for the task to finish but you can use the following script for that. Save the content to a file named `Run-Task.ps1` and execute it with the command `.\Run-Task.ps1 "Task Name"`.

```powershell
param(
    [string]$taskName
)

# Get the task
$task = Get-ScheduledTask $taskName
$sleepIntervalInSeconds = 1


if ($task.State -ne "Running")
{
	Write-Host "Start $taskName (Current state: $($task.State))"
	# Start the task
	$task | Start-ScheduledTask
}
else
{
	Write-Host "Task $taskName is already running (Current state: $($task.State))"
}

# Wait for the task to complete
$task = Get-ScheduledTask $taskName
while ($task.State -eq "Running")
{
    Write-Host "Waiting for $taskName to finish..."

    Start-Sleep -Seconds $sleepIntervalInSeconds
    $task = Get-ScheduledTask $taskName
}

Write-Host "Completed $taskName (Current state: $($task.State))"
```