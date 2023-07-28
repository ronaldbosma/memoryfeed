# SQL Server

## Config tools
Config tools that can be used to e.g. configure SQL aliases.

### Client machine

These tools can be found on a 'client' machine that connects with SQL server

32-bits `C:\Windows\system32\cliconfg.exe`  
64-bits `C:\Windows\syswow64\cliconfg.exe`

### SQL Server

Microsoft Management Console snap-in for SQL Server 2017 configuration `SQLServerManager14.msc`. 

## Run in Docker container

https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-powershell


Get SQL Server container image
	
```
docker pull mcr.microsoft.com/mssql/server:2022-latest
```


Start SQL Server container 
> I was unable to login to the SQL Server from outside of the container, because I already had a SQL Server instance running on 1433. So I changed the outside port from 1433 to 14333, as is shown in the example below.

```
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Welkom12345!" `
    -p 14333:1433 --name sql1 -h sql1 `
    -d mcr.microsoft.com/mssql/server:2022-latest
```
   

Change password
```
docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
    -S localhost -U SA -P "Welkom12345!" `
    -Q "ALTER LOGIN SA WITH PASSWORD='Welkom123456789!'"
```

Connect to SQL Server from inside container
```
docker exec -it sql1 "bash"
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Welkom123456789!"
```	

Connect to SQL Server from outside container
```
sqlcmd -S 127.0.0.1,14333 -U SA -P "Welkom123456789!"
```