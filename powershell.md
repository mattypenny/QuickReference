---
title: "powershell"
date: "2012-12-25"
draft: false
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.

This ramshackle collection of Powershell commands evolved from my own quick-reference crib-sheets over the last couple of years.

## Get code
### Show loaded Powershell function code

```
get-command get-pins | format-list definition
```

### Show loaded Powershell function code for aliases too
```

$Command = get-command "sss"
$commandType = $Command.CommandType
if ( "$commandType" -eq "Alias" ) { $Command = get-command $Command.ResolvedCommandName}
$Command.Definition
```

### Show sqlserver stored procedure code
```
dir SQLSERVER:\SQL\$Computername\default\Databases\_DBA_CATALOG\StoredProcedures | select Name, DateLastModified, TextBody | format-list
```
## Modules

### Show module path
```
$env:PSModulePath
```
### Show available modules
```
get-module -ListAvailable | select Path
```

## Get file and folder information

### Get the size of all the files in a folder
```
dir \\server01\t$\sql_trace | measure-object -property length -sum
```
### List contents of folder ordered by time
```
dir c:\tax_dodgers | sort-object -property lastwritetime
```
or, for latest first
```
dir c:\tax_dodgers | sort-object -property lastwritetime -descending
```

### List files modified within the last three hours
```
dir -recurse \\server1\c$ -File | ? lastwritetime -gt $(get-date).AddHours(-3) | select fullname, lastwritetime
```

### List files modified between 2 dates
```
gci -recurse $Filespec | select lastwritetime, length, fullname | 
        where-object {
                        $_.Lastwritetime -ge (Get-Date).AddDays(-14) -and 
                        $_.Lastwritetime -le (Get-Date).AddDays(-7)
                     }
```

### List files in sub-folders showing only part of the path

```
dir  C:\Users\matt\OneDrive\music  -recurse -exclude *xmas*  -file | ft @{Expr = {$_.fullname -replace "C:\\users\\matt\\OneDrive\\music", ""}}

```

### System.IO stuff
```
[System.IO.Path]::GetPathRoot("c:\temp\elvis.txt")                 # Gets the root directory 
[System.IO.Path]::GetRandomFileName()                              # Returns a random folder name or file name.
[System.IO.Path]::GetTempFileName()                                # Creates a uniquely named, zero-byte temporary file 
[System.IO.Path]::GetTempPath()                                    # Returns the path of the current users temporary folder.
[System.IO.Path]::HasExtension("c:\temp\elvis.txt")                # Determines whether a path includes a file name extension.
[System.IO.Path]::IsPathRooted("c:\temp\elvis.txt")                # Returns true if specified path string contains a root.
[System.IO.Path]::ChangeExtension("c:\temp\elvis.txt", "csv")      # changes the extension
[System.IO.Path]::Combine("c:\temp", "elvis.txt")                  # Combines two strings into a path.
[System.IO.Path]::GetDirectoryName("c:\temp\elvis.txt")            # Returns the directory 
[System.IO.Path]::GetExtension("c:\temp\elvis.txt")                # Returns the extension 
[System.IO.Path]::GetFileName("c:\temp\elvis.txt")                 # Returns the file name and extension
[System.IO.Path]::GetFileNameWithoutExtension("c:\temp\elvis.txt") # Returns the file name without the extension.
[System.IO.Path]::GetFullPath("c:\temp\elvis.txt")                 # Returns the absolute path 

```



### Show lines appended to a file as they get appended

```
Get-Content -Path C:\fso\mylogfile.log -Tail 1 -Wait
```

### Show first 'n' lines of a file
```
sshare whatever | select-object -first 3     # head
```
### Show last 'n' lines of a file
```
Get-Content [filename] | Select-Object -Last 10 # equivalent of tail
```
or
```
Get-Content -Path C:fsomylogfile.log -Tail 1 -Wait
```
### Does file in one folder exist in another
```
foreach ($file in $(dir '\\server1\d$\dbawork\matt\bin\*.ps1')) { $f = $file.name ;  "$f ; $(Test-path c:\powershell\functions\$f)"}
```
 
### Map a drive
```
New-PSDrive -Name "P" -PSProvider FileSystem -Root "\\$ComputerName\personal\matt" -Persist
 
```
### Looks for occrurences of 'string' in file
```
select-string elvis top1000.txt
```
### Match one word or another
```
select-string -pattern "select-str|array" C:\Users\matt\Documents\QuickReference\*.md
```
### Return true or false if string is in a file
```
select-string -quiet -Pattern "^$ComputerName( |$)" -path $File
```
### Get file security information
```
$X = dir c:\inetpub\wwwroot\wpresources\web.config 
$X.getaccesscontrol().Access | select identityreference, Filesystemrights
```
or
```
get-acl "\\$ComputerName\data\it\folder1" | select accesstostring
```

### Show last modified time, reformatting the time
```
dir "\\server\f$\other server backups\APP2*" | select @{Label="LastUpdate"; Expression = {"{0:dddd dd MMMMM yyyy HH:mm}" -f $_.lastwritetime } }
```

### Show sql filesizes in Mb
```
Invoke-Sqlcmd -ConnectionTimeout 1 -Query "SELECT sum(size*8/1024) SizeInMb from sysfiles" -ServerInstance $I -database $D
```

### Show Sqlserver table sizings in Powershell
```
dir SQLSERVER:\SQL\$ServerInstance\Databases\$DatabaseName\Tables | select DisplayName, rowcount, dataspaceused, indexspaceused | ft -a
```

### Show mdb files on drive
```
dir -recurse "\\$ComputerName\C$" *.mdb | select fullname, length, lastwritetime | where-object {$_.fullname -notlike "*Seed*"} | ft -a
```



### Show unique lines in file
```
gc c:\temp\2000.txt | gu
```

### Get number of lines, words and characters in a file
```
gc c:\temp\file.txt | measure-object -line
gc c:\temp\tweets.csv | measure-object -words
gc c:\temp\tweets.csv | measure-object -Character

```


### Get the sum of the sizes of specified files
```
dir \\$ComputerName\d$\mssql_backup -recurse *1410071800.bak | measure-object -sum length
```

 
### Get databases and their filegroups
```
dir sqlserver:\sql\$ComputerName\qc01\databases | select name, filegroups
```
or
```
dir sqlserver:\sql\$ComputerName\qc01\databases\db1\FileGroups | select -expandp files | select filename
```


### Get a directory listing across multiple servers
```
foreach ($Server in ("$ComputerName1","$ComputerName2","$ComputerName3")){dir \\$server\c$}
```


### Find files across multiple servers
```
foreach ($Server in ("$ComputerName","$ComputerName", "$ComputerName")

{
dir "\\$Server\c$" -recurse tnsnames.ora | select fullname
dir "\\$Server\d$" -recurse tnsnames.ora | select fullname
}
```
### Get database details into a csv file
```
dir sqlserver:\sql\$ComputerName\qc01\databases | select * | convertto-csv > c:\temp\qc_databases.csv
```


 
### Get data file names
```
dir sqlserver:\sql\$ComputerName\qc01\databases\db1\FileGroups | select -expandp files | select filename
```

### Get transaction log file names
```
dir sqlserver:\sql\$ComputerName\qc01\databases\db1\logfiles | select filename
```


 
### Show files modified in the last 2 days
...excluding the cache.
```
find . -mtime -2 -exec ls -ld {} \; | egrep -v cache
```
## Scheduled jobs

### Run a sql job
```
$JOB = dir Sqlserver:\sql\$Computername\default\Jobserver\jobs | where-object {$_.name -like '*big-job*'}
$JOB # check what youve got!
$JOB.start()
```

### Get sql jobs
```
dir Sqlserver:\sql\$Computername\default\Jobserver\Jobs | select name, lastrundate, nextrundate, currentrunstatus, lastrunoutcome | ft
```

### Get sql jobs - prettier
```
dir Sqlserver:\sql\$Computername\default\Jobserver\Jobs | ft @{Label ="Jobbie" ; Expression={$_.name} ; Width = 42 }, @{Label="Last run" ; Expression = {"{0:dd/MM/yy HH:mm}" -f $_.lastrundate} ; width=14}, @{Label="Now"; Expression = {$_.currentrunstatus} ; Width = 5}, lastrunoutcome
```
 
### Get a list of maintenance sub plans
tbc: Needs more work!
```
sql $ServerInstance "SELECT * FROM msdb.dbo.sysmaintplan_subplans"
```

### Get the job step detail
```
dir Sqlserver:\sql\$ServerInstance\Jobserver\Jobs | 
    ?  name -eq "my beautiful job" | 
    select -expand jobsteps | 
    select Parent, name, command, lastrundate, lastrunduration, outputfilename
```
### Script out all the jobs
```
foreach ($Job in dir Sqlserver:\sql\server1\default\Jobserver\jobs) {$JobName = $Job.Name; $Job.Script() > c:\temp\server1\$JobName.sql}
```
### Import a scripted out job
```
invoke-sqlcmd -ServerInstance server2 -InputFile "C:\temp\server1\Working Hours.sql"
```

## Scripting

### if-elseif-else
```
if ($N -gt 2) {"N gt 2"} elseif ($N -lt 2) {"N lt 2"} else {"Neither"}
```
### while loop
```
$N = 0
while ($N -le 365) { $N += 7; $(get-date).adddays(3 + $N) }
```

### foreach loops
```
$start = get-date "04-Mar-2015 12:55:00"
$end = get-date "04-Mar-2015 13:10:00"
$HyperionServers = "$ComputerName $ComputerName $ComputerName $ComputerName $ComputerName $ComputerName $ComputerName $ComputerName $ComputerName"
 
$Logs = foreach ($S in $HyperionServers.split())
{
$Logtypes = "Application System"
 
foreach ($L in $Logtypes.split())
{
get-eventlog -computername $S -logname $l -after $start -before $end
# select TimeGenerated, MachineName, EntryType,Message | ft -a
}
}
```

foreach thing in a list of things
```
$Q = "ALTER SERVER ROLE [sysadmin] ADD MEMBER [domain\user];"; foreach ($S in $(import-csv .\data\ListOfServerInstances.csv)) {[string]$SI = $S.ServerInstance; invoke-sqlcmd -serverinstance $SI -query $Q}
$Q = "select @@servername; ALTER SERVER ROLE [sysadmin] ADD MEMBER [domain\user];"; foreach ($S in $(import-csv .\data\ListOfServerInstances.csv)) {[string]$SI = $S.ServerInstance; invoke-sqlcmd -serverinstance $SI -query $Q}

```

### try-catch
```
try { } catch { }
 
```

## Miscellaneous
### Generate 'git add' command in Powershell
```
write-host -nonewline "git add" ; foreach ($F in $(get-gitstatus | select -expand working)) {write-host -nonewline " $F"}
```
### Generate 'git add' command in bash
```
git status --porcelain | awk 'BEGIN {printf("git add ") }  {printf("%s ", $2)}'
```
### When rdp just shows a black screen
```
Ctrl-Alt-End - resolves rdp black screen
```
### Shows people connected through remote desktop
```
query session /server:server1
```

### To kill off someone else's rdp session
```
reset session 1 /server:server01
 
```

### Show today's date
```
get-date
Get-Date -format "dd MMM yyyy HH:mm:ss:fff"
Get-Date -format "yyyyMMddHHmmssfff"
```
### Get performance counters
```
Get-Counter -Counter '\MSSQL$INST01:SQL Statistics\Batch Requests/sec' -ComputerName $ComputerName
```
### Get last veeam backup
```
Add-PSSnapin VeeamPSSnapIn 
Get-VBRBackupSession | ? JobName -like '*sql*' | ? endtime -gt $(get-date).adddays(-2) | select jobname, creationtime, endtime, iscompleted, result | ft -AutoSize

```

## Privileges

### Grant alter trace
```
grant alter trace to login
```

### Grant read procedure code
```
GRANT VIEW DEFINITION ON procedurename TO username
```

### Dynamic SQL to grant read procdure on all procedures
```
select 'GRANT VIEW DEFINITION ON ' + quotename(specific_schema)
+ '.' + quotename(specific_name)
+ ' TO ' + 'loginname'
from INFORMATION_SCHEMA.routines
where routine_type = 'PROCEDURE'
order by SPECIFIC_NAME
```


## Personalization
### Change the Powershell prompt to a dollar prompt
```
function prompt { "$ " }
```

### Get a list of aliases
```
get-alias | select name, definition |` ft @{Label="Aliases" ; Expression = {$_.name + ", # " + $_.Definition} }
```
### Remove an alias
```
Remove-Item alias:write-warning
```

### Get your last 100 commands
```
history -count 100 | ft -property CommandLine
```


### Split your path into an array of folders
```
$a = $env:path; $x=$a.Split(";") 
```


### Get the location of the documents folder
```
[environment]::getfolderpath('Mydocuments')
```


### Send an email with Exchange
```
$PSEmailServer = "$ComputerName.company.co.uk"
send-mailmessage -to matt.penny@company.co.uk -from jose@cfc.com -subject "This email proves that mail works from sql006"

```
### Get emails
```
$olFolders = "Microsoft.Office.Interop.Outlook.olDefaultFolders" -as [type]  
$outlook = new-object -comobject outlook.application 
$namespace = $outlook.GetNameSpace("MAPI") 
$folder = $namespace.getDefaultFolder($olFolders::olFolderSentMail) 
$folder.items |  ? subject -like "*What I did*" | ? to -like "*Fred*" | select senton, body | fl | clip
``` 

### Send an enail with smtp
```
$smtp = New-Object Net.Mail.SmtpClient("$ComputerName.company.co.uk")
$smtp.Send("rui.faria@cfc.com","matt.penny@company.co.uk","This ones via smtp","This is a test"
```

 
### Dump all of Powershell help into a text file
```
"Help text file generated $(get-date)" | out-file c:\temp\posh_help.txt
$HelpItems = get-help * | select name
foreach ($H in $HelpItems) {get-help -full $H.name | out-file -append c:\temp\posh_help.txt }
-- dump help into txt file start
```
### Get all the synopses for the functions in a module
```
get-command | where-object module -like "My-Module" | foreach {get-help $_} | ft -AutoSize name, synopsis
```

Via https://blogs.technet.microsoft.com/heyscriptingguy/2015/04/21/powertip-find-powershell-cmdlets-and-synopsis/

### Search command history for a string
```
history | select commandline | where-object {$_.commandline -like '*elvis*'} | fl
```

### Save Powershell session to a file
```
start-transcript
stop-transcipt
```
### Change the title bar of your Powershell window
```
$host.ui.rawui.WindowTitle="I've got the power"
```
### Get username
```
[Security.Principal.WindowsIdentity]::GetCurrent() | select name
``` 

### git stuff
```
git clone https://gist.github.com/b271556bb0f771d39a65.git one-liner
git add inv*.txt
git commit -m 'Updates to investigate.txt'
git push origin master
git pull
```

## Servers

### Get list of PCs from AD
```
Get-ADComputer -filter { OperatingSystem -NotLike '*Windows Server*' } -properties * | 
            select DNSHostName,
            IPv4Address,
            IPv6Address,
            LastLogonDate,
            Modified,
            Name,
            OperatingSystem,
            OperatingSystemHotfix,
            OperatingSystemServicePack,
            OperatingSystemVersion
```

### get ip-address from pinging computer
```
$PingObject = test-connection -computerName $ComputerName  -Count 1 -ErrorAction stop
[string]$IPAddress = $PingObject.IPV4Address
```

### Show re-boots
Not _entirely_ sure whether this covers server crashes, or just deliberate re-starts. Need to look at event code 1074 further...or find a code for the start-up rather than the shutdown
```
gwmi -computername $ComputerName win32_ntlogevent -filter "LogFile='System' and EventCode='1074'" | select User,@{n="Time";e={$_.ConvertToDateTime($_.TimeGenerated)}} | ft -AutoSize
```
### Show eventlogs
```
$start = get-date "07-Dec-2012 15:00:00"
$end = get-date "07-Dec-2012 16:00:00"
get-eventlog -computername bigserver -logname Application -after $start -before $end | select timewritten, message

```
### Get processes where the proceess name is like "*sql*"
```
get-process | where {[Regex]::Ismatch($_.name.tolower(), "sql") }
```


### Get processes using more than 1MB of memory
```
get-process | where {$_.PeakWorkingSet -gt 1Mb } | select ProcessName,PeakWorkingSet
```

### Get processes whose name matches a specified name on a remote server
```
get-process -ComputerName $Computername Var* .
```

### Get processes in the order of how much memory they are using
```
get-process -ComputerName $Computername | sort-object VirtualMemorySize
```

### Get processes with a speified name
```
get-process oracle
```

### Get threads for a specified process
```
get-process oracle | select ProcessName -expand Threads | select Id,StartTime,TotalProcessorTime, WaitReason
```

### Get startup time for a process
```
get-process oracle | select ProcessName, StartTime
```


### Get process with amount of memory used
```
get-wmiobject win32_process -computername $Computername | select Name, workingsetsize | sort name
```

### Get process with CPU used
```
get-wmiobject win32_process -computername $Computername | where {[Regex]::Ismatch($_.name.tolower(), "sql") } | select Name, kernelmodetime, usermodetime | sort name
```

### Get services with current state
```
get-wmiobject win32_service -computername $Computername | select name, DisplayName, state, status
```

### Get a list of connected USB devices
```
gwmi Win32_USBControllerDevice
```

### Get a list of some installed products
```
Get-WmiObject -Class Win32_Product | Select-Object -Property Name
```
This command lists _some_ installed products. Tbh I don't have a clear idea of what's included and what's not. On my PC, it shows this:

A slight remix is:

```
get-wmiobject -class "Win32_Product" -namespace "root\CIMV2" -computername "." | select Name, Vendor, Version | sort-object name | ft -a
```

### Get a list of shares
```
Get-WmiObject Win32_Share -ComputerName $Computername | Select Name, Path, Type | FT
```


### Get the size of a database
tbc - need to check this
```
Invoke-Sqlcmd -ConnectionTimeout 1 -Query "SELECT sum(size*8/1024) SizeInMb from sysfiles" -ServerInstance $I -database $D
```


### Start up a sql server agent
```
$(get-service -ComputerName $ComputerName SQLSERVERAGENT).start()
```



### Get whats running now on a sql instance
```
sql $ServerInstance "select er.session_id,
er.database_id,
er.command,
er.blocking_session_id,
er.wait_type,
er.wait_resource,
st.text
from sys.dm_exec_requests er
outer apply sys.dm_exec_sql_text (er.sql_handle) st
where st.text is not null"
```
..where 'sql' is an alias for a wrapper to invoke-sqlcmd 
### Get waiting processes
```
sql $ServerInstance "select wt.*,
st.text
from sys.dm_os_waiting_tasks wt
left join sys.dm_exec_requests er
on wt.waiting_task_address = er.task_address
outer apply sys.dm_exec_sql_text (er.sql_handle) st
where st.text is not null
order by wt.session_id "
```

 

### Get sql sessions
```
sql $ServerInstance "select session_id, start_time, status, command, database_id d, user_id u, blocking_session_id b, wait_type, wait_time, last_wait_type, wait_resource, percent_complete, estimated_completion_time, cpu_time, total_elapsed_time, reads, writes, logical_reads, transaction_isolation_level,query_hash, query_plan_hash from sys.dm_exec_requests" | ft -a
```

 
### Get sql sessions - 2
```
sql $ServerInstance "select session_id, start_time, wait_resource wres, percent_complete '%', estimated_completion_time c, cpu_time cpu, total_elapsed_time ela, reads rd, writes wr, logical_reads lrd, transaction_isolation_level til from sys.dm_exec_requests" | ft -a
```

 
### Get accumulated waits
```
sql $ServerInstance "select * from sys.dm_os_wait_stats where waiting_tasks_count > 0" | ft -a
```

 
### Get missing indexes
```
sql $ServerInstance "select statement, equality_columns, included_columns from sys.dm_db_missing_index_details where statement like '%whatever%'"
```

### Get missing indexes and stats
```
select ddmigs.user_seeks, user_scans, unique_compiles, coalesce(last_user_seek,last_user_scan), avg_total_user_cost,avg_user_impact,
       ddmid.index_handle, DB_NAME(database_id), statement,  equality_columns, inequality_columns, included_columns, OBJECT_NAME(object_id)
from sys.dm_db_missing_index_details ddmid,
     sys.dm_db_missing_index_groups ddmig,
     sys.dm_db_missing_index_group_stats ddmigs
where ddmig.index_group_handle = ddmigs.group_handle
and ddmid.index_handle = ddmig.index_handle
-- order by ddmigs.last_user_seek desc
order by avg_user_impact + avg_system_impact desc
```

 
### Get waits
```
sql $ServerInstance "select * from sys.dm_os_waiting_tasks" | select session_id,wait_duration_ms,wait_type,blocking_session_id, resource_description | ft -a
```

## Processes

### Show sql sessions using sp_whoisactive
sp_whoisactive is a widely-used stored proc downloadable <a href="http://sqlblog.com/files/default.aspx">here</a>. I install it into every master database.
```
sql $ComputerName "EXEC sp_WhoIsActive @show_sleeping_spids = 2" | select session_id, login_name, wait_info,CPU,blocking_session_id,reads,percent_complete,host_name,database_name,start_time,login_time
```
### Show sql sessions using sp_whoisactive wwith blocking session
```
sql $ServerInstance "EXEC sp_WhoIsActive @find_block_leaders = 1, @show_sleeping_spids = 2" | select session_id, login_name, wait_info, blocking_session_id, host_name,  program_name, database_name | ft -AutoSize
```
 
### Start specified service
???tbc
I haven't used this for some time - I need to try it out
```
set-service winrm -status Running -passthru -computername Server02
```

### Pause specified services
???tbc
I haven't used this for some time - I need to try it out
```
get-service schedule -computername S1, S2 | set-service -status paused
```

 
### Get a list of where connections to sql servers are coming from

```
foreach ($i in $(smi)) { $ServerInstance = $i.instance_name ; ""; ""; " ***** Sql host: $ServerInstance *****" ; sql $ServerInstance "select distinct hostname from sysprocesses"}
```
'smi' here is an alias which returns a list of sql instances which I look after
 

### Restart computer
```
restart-computer -computername $ComputerName -force
```



 
## Logs

### Get sqlserver errorlogs
```
$logs = dir sqlserver:\sql\$Computername # readerrorlog

$logs.readerrorlog(1) <--- where (1) means last-but-one
$logs.readerrorlog(0) | where logdate -gt ([datetime]::ParseExact('25/02/2015 07:27:36',"dd/MM/yyyy HH:mm:ss",$null)) | select processinfo, text | ft -a -wrap
```


### Get event logs
```
$end = get-date "07-Dec-2012 16:00:00"

$start = get-date "07-Dec-2012 15:00:00"
get-eventlog -computername $Computername -logname Application -after $start -before $end | select timewritten, message
```
 
### Get most recent eventlogs
```
get-eventlog -computername $Computername -logname application -newest 15
```

 
### Get sqlserver errorlog
```
-- reading errorlog - start
declare @LogsSince datetime
declare @TimeNow datetime
 
select @LogsSince = dateadd(day, datediff(day, 0, getdate()),0) -- start of day
select @LogsSince = dateadd(hour, -1, getdate()) -- last hour
 
select @TimeNow = getdate()
 
exec xp_readerrorlog 0, 1, null, null, @LogsSince, @TimeNow
-- reading errorlog - end

```


## Software
### Get sql version and edition
Not quite sure how this works if you have different instances with different versions of the software one one server, tbh
```
foreach ($INSTANCE_NAME in ("tom", "jerry", "spike")) { dir SQLSERVER:\SQL\$INSTANCE_NAME | select name, version, edition }
```

### Get database compatability level and other details
```
dir SQLSERVER:\SQL\$ComputerName\default\Databases | select name, status, recoverymodel, CompatibilityLevel, createdate, dataspaceusage, readonly, size | ft -a
```

### Get name of the sql .dll
```
foreach ($PC in ("xbox04", "xbox05", "xbox0") ) { dir \\$PC\c$\windows\syswow64\sql*.dll | select fullname }

```

### Get windows version and service packs
```
get-wmiobject -class win32_operatingsystem -computer $ComputerName |
    select __Server,
           caption,
           Manufacturer,
           InstallDate,
           LastBootUpTime,
           Version,
           ServicePackMajorVersion,
           ServicePackMinorVersion
```


## Strings
### Prompt the user to input a string
```
$small_number = read-host "How much tax did Google pay in 2011? "
```
### Prompt the user to input a string, but dont echo it back
```
$small_number = read-host "How much tax did Google pay in 2011? " -assecurestring
```
### Return true if string contains a pattern
```
[Regex]::Ismatch($a, "[ADB]um")
```
### Concatenate sting with carriage return
```
$RestoreString = $RestoreString + [char]10 + "move N'" 
 
```
### Split string into an array of words
```
$a = $env:path; $x=$a.Split(";") 
```
### Return position of one string within another
```
$a = "The famous cfc"
$a.IndexOf("cfc")
```
Note that the first character is character 0
### Get length of string
```
$a.length
```
### Replace one string with another
```
$a.replace("cfc", "Chelsea FC")
```
### Get a specified part of a string
```
$a.substring(7,3)
```
### Get the upper-cased string
```
echo $a.ToUpper()
```
### Split a string
```
$X="John Terry\Ashley Cole"
$XX=$X.split("\")[0]
```


### Replace strings in a file
```
select-string h3 c:\temp\one.txt | ft @{Expr= {$_.line -replace "### ","" -replace "","" }}
```

### Surround current line with backticks and insert a header above
```
map B O### tbc<CR>```<Esc>jo````<CR><Esc>
```

### Extract a server name from a list of instance names
```
foreach ($i in $(smi)) { $ServerInstance = $i.instance_name ; $SplitSI=$ServerInstance.split("\") ; $Server = $SplitSI[0] ; $Server}
```
### Get a folder list for the c: drive of the host for a list of sqlserver instances
```

foreach ($i in $(smi))
{
  $ServerInstance = $i.instance_name
  $SplitSI=$ServerInstance.split("\")
  $Server = $SplitSI[0]
  dir "\\$Server\c$"
}



```



## Users, logins, security

### Get a list of users
```
get-aduser -Filter {name -like "*matty*"} | select name,  samaccountname, objectclass, Enabled
```

### Get members of group
 
```
get-adgroupmember sql_dba
```

### Show groups of which user is a member
```
get-aduser jbloggs -properties memberof | select -expand memberof | sort
```
### Show user details
```
get-aduser jbloggs -properties * 
```
 
### Show locked users
```
search-adaccount -LockedOut | select name, samaccountname
```
 
### tbc
```
dir Sqlserver:\sql\$ComputerName\default\databases\db1\users\td | select -expandproperty extendedProperties
```
### tbc
```
foreach ($db in dir Sqlserver:\sql\$ComputerName\default\databases\)
{
$D=$db.name
write-host $D
dir "Sqlserver:\sql\$ComputerName\default\databases\$D\users"
}
```
### Get instance logins
```
 
dir Sqlserver:\sql\$ComputerName\qc01\logins | ft -a
``` 
or
```
dir SQLSERVER:\SQL\$Computername\default\logins | select Name, CreateDate, DefaultDatabase, IsDisabled, IsLocked, IsPasswordExpired | ft -a
```
or
```
dir Sqlserver:\sql\$ComputerName\default\logins | select name, logintype, isdisabled, islocked, ispasswordexpired, hasaccess
```
#### Get sql server database logins

```
dir SQLSERVER:\SQL\$Computername\prod01\databases\remedi\users
```

#### Get sql server database users and their roles
```
$DatabaseUsers = dir Sqlserver:\sql\$ServerName\$InstanceName\databases\$DatabaseName\Users
foreach ($User in $DatabaseUsers) {"==$User=="; $User.EnumRoles()}  
```



### tbc
```
$computer = [ADSI]"WinNT://$ComputerName,computer" ; $computer.psbase.Children | Where-Object { $_.psbase.schemaclassname -eq 'user' } | select name
 
```

#### Active Directory stuff
tbc - need to check this
```
import-module ActiveDirectory
help ActiveDirectory
get-psprovider
dir activedirectory
dir activedirectory:
dir AD:
dir AD:\company
dir AD:\DC=company,DC=co,DC=uk
dir AD:\DomainDnsZones

```
### Get a list of subnets

```
System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest().Sites | select -ExpandProperty subnets
```

## Lists, arrays, etc
```
$HardCodedList = "ANONYMOUS LOGON","SYSTEM","LOCAL SERVICE","NETWORK SERVICE" 
if ($HardcodedList -contains $Username)
{
    write-Debug "$Username is hard-coded out"
    $UsernameIsOnTheExclusionList = $True
}
```

### Array
arrays
```
$UserNameArray = @()
$UserNameArray += $Username
```

### Convert array into object
```
$UserNameObject = foreach ($Username in $UserNameArray) { [pscustomobject]@{Username = $Username} }
```

<<<<<<< HEAD
## Pester

### Not actually Pester, but handy
```
mock Get-ADComputer {                                                                                #Pester
                                                                                                     #Pester
    $Csv=@"                                                                                          #Pester
        "Name","IPv4Address","LastLogonDate","OperatingSystem"                                       #Pester
        "Glooper.cfc.co.uk","Glooper",,"12/12/2016 14:02:57","Windows 7 Professional"                #Pester
        "Drooper.cfc.co.uk","Drooper","10.11.201.194","31/01/2017 07:50:31","Windows 7 Professional" #Pester
"@                                                                                                   #Pester
    $Csv | convertFrom-csv                                                                           #Pester
} -Module RestartPCs                                                                                 #Pester








```

### Standard-ish stuff


```
$here = Split-Path -Parent $MyInvocation.MyCommand.Path                                            #Pester
$module = [System.IO.Path]::GetFileNameWithoutExtension( $MyInvocation.MyCommand.Path)             #Pester
$module = $module.replace('.Tests','')                                                             #Pester
$PesterData = "$here\PesterData"                                                                   #Pester

remove-module $module                                                                              #Pester
import-module $module                                                                              #Pester

function todo {                                                                                    #Pester
    param ([string]$TodoText)                                                                      #Pester
    write-host -foregroundcolor DarkYellow " [o] Todo: $TodoText"                                  #Pester
}                                                                                                  #Pester


Describe "the pester test" {                                                                                                            #Pester
    It "has no 'if..=' clauses" {                                                                                                            #Pester
        $(select-string -pattern "if.*=" diagnostics\*\*.Tests.ps1 | measure-object).count | Should Be 2 #Pester
    }                                                                                              #Pester
}                                                                                                  #Pester

mock Get-ADComputer { return "Gloopy" }  -Module RestartPCs                                       #Pester



```


### Actually Pester

```
$($PCDetails | where ComputerName -eq 'Ordinator' | measure-object).count | Should Be 1            #Pester

{[datetime]$RetrievedDate = [datetime]::ParseExact($LastFound,'dd/%M/yyyy H:%m:%s',$null)} | Should Not Throw   #Pester
$RetrievedDate | Should BeLessThan $(get-date)                                                     #Pester

{get-aduser $username} | should not throw                                                          #Pester

$Contacts | should not be $PreviousContacts                                                        #Pester
$(compare-object $UniqueSortedPCNames $SortedPCNames | measure-object).count | should be 0         #Pester
It "Contains 700 to 900 records" {                                                                 #Pester
    $($PCDetails | measure-object).count | Should BeLessThan 900                                   #Pester
    $($PCDetails | measure-object).count | Should BeGreaterThan 700                                #Pester
}                                                                                                  #Pester


```



## To sort

### New Powershell Object (old)
```
new-object PSObject -Property @{ Command = $Command;Output = $Output; Comment = $Comment}
```
### New Powershell Object (new)
```
[PSCustomObject]@{ ComputerName = $ComputerName; Status = $ComputerStatus }
```
### import a csv file
```
$CSVObject = import-csv -LiteralPath $CsvFile -Header ReadyDate, MandatoryDate -ErrorAction Stop
```

#### Look for files which dont match the format '1st-whatever'
```
dir  | 
    ? name -notlike "[0-9][snrt][dht]-*" | 
    ? name -notlike "[123][0-9][snrt][dht]-*"
```

#### Diff between two files
```
Compare-Object -ReferenceObject (Get-Content file1) -DifferenceObject (Get-Content file2)
```

#### Get data from .csv into powershell
```
$CSVObject = import-csv -LiteralPath $BootDateFile -Header RebootReadyDate, RebootMandatoryDate
```
#### Convert string to date 
The context here is getting the last boot up time from WMI
```
$WmiObject = get-wmiobject -class win32_operatingsystem -computer $ComputerName 
$LastBootupString = $WmiObject.LastBootupTime
$LastBootupDateTime = [System.Management.ManagementDateTimeConverter]::ToDateTime($LastBootUpString)
```
#### Start the git-shell
There are probably much better ways of doing this
```
C:\Users\matt\AppData\Local\GitHub\GitHub.appref-ms --open-shell
```
#### Get to git through proxy

```
$proxy = new-object System.Net.WebProxy
$proxy.Address = "http://WhateveWhatever.whatever.co.uk:portno"
$proxy.useDefaultCredentials = $true
```

##### Or....

First, get the proxy from the browser. 
i.e.firefox >> options >> advanced >> network >> settings and get the http Proxy and the Port.

Then:
```
$HTTP_PROXY="<http proxy from firefox>:<port from firefox."

```
Then set the proxy in the git config

```
git config --global http.proxy $HTTP_PROXY

# can check with
git config --get http.proxy
```


Then you need to set the certificate info(?). Found a note saying to do this:
```
git config --system http.sslcainfo "C:\Program Files (x86)\git\bin\curl-ca-bundle.crt"
```
....but my curl-ca-bundle.crt wasnt there (or anywhere similar) so I did a search for it and then plugged in the one I found

.... and it worked
```
C:\Users\matt\Documents\GitHub> git clone https://gist.github.com/b271556bb0f771d39a65.git
Cloning into 'b271556bb0f771d39a65'...
remote: Counting objects: 129, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 129 (delta 0), reused 0 (delta 0), pack-reused 126
Receiving objects: 100% (129/129), 41.53 KiB | 0 bytes/s, done.
Resolving deltas: 100% (40/40), done.
Checking connectivity... done.

```

#### Show function code - also shows alias definitions
```
(Get-Command show-os).Definition
```
#### Very unsorted
```
dir -attributes directory

```






## IDEs

### ISE

#### Snippets

```
cd C:\users\matt\Documents\WindowsPowerShell\Snippets


Remove-item "Pester - test-ShWebPage.snippets.ps1xml" 
New-IseSnippet -Title "Pester - test-ShWebPage" -Description "Pester It clause for test-ShWebPage" -Text @"

It "The xx web page is available" {

    test-ShWebPage -WebPage "xx" | Should Be `$True

}
"@



```

```
$Snippy = @"
function verb-noun { 
<#
.SYNOPSIS
    One-line description
.DESCRIPTION
    Longer description
.PARAMETER

.EXAMPLE
    Example of how to use this cmdlet
#>
    [CmdletBinding()]
    Param
        (
            [string]$Param1,
    
            [int]$Param2
        )
    write-startfunction



    write-endfunction

}
"@

New-IseSnippet -Title "Cmdlet (Simple)" -Description "Matt's simple function template" -Text $Snippy
```

### For loops

```
For ($i=0; $i -le 8; $i++) 
```

## Splatting

```
$Params = @{'From'='tinkerbell@LifeIsAButADream.co.uk';   # splatting
            'To'='matt.penny@LifeIsAButADream.co.uk';     # splatting
            'Subject'='LifeIsButADream';                  # splatting
            'Body'='LifeIsButADream'                      # splatting
            'Attachments'= "c:\temp\testy_mctestface.txt",# splatting
                           "c:\temp\monkeys.txt"          # splatting
            'SmtpServer'="xx"                             # splatting
            'Encoding'="ascii"                            # splatting
            'BodyAsHtml'=$True                            # splatting
           }                                              # splatting
                                                          # splatting
send-MailMessage @Params                                  # splatting


```
## Veeam/VBR

```
Add-PSSnapin veeampssnapin                                                           # veeam/vbr
Get-command *vbr*                                                                    # veeam/vbr
Connect-VBRServer -Server $OurVeeamServer                                            # veeam/vbr
Get-VBRBackup -Name ${TargetServer}_full | select Name, dirpath, metafilename        # veeam/vbr
get-vbrjob -Name *whatever*                                                          # veeam/vbr
get-vbrjob -Name *whatever* | select -ExpandProperty ScheduleOptions                 # veeam/vbr
                                                                                     # veeam/vbr
Get-VBRBackupSession | ? JobName -like '*whatever*' |                                # veeam/vbr 
                       ? endtime -gt $(get-date).adddays(-2) |                       # veeam/vbr
                       select jobname, creationtime, endtime, iscompleted, result |  # veeam/vbr 
                       ft -AutoSize                                                  # veeam/vbr

 




$Backups = Get-VBRBackupSession | ? JobName -like '*whatever*' | ? endtime -gt $(get-date).adddays(-2) # veeam/vbr
$Backups | select jobname, jobtype, creationtime, endtime                                              # veeam/vbr
$Jobs = Get-VBRJob | ? Name -like '*whatever*'                                                         # veeam/vbr
$Jobs | select name, targetdir,targetfile, sqlenabled, oracleenabled                                   # veeam/vbr
Get-VBRJobObject                                                                                       # veeam/vbr
Get-VBRJobObject -Job $Job | select name, location, approxsizestring                                   # veeam/vbr



```

## Params

```
[CmdletBinding()]                                                             # Params
Param( [string]$SimplePath = "$CheckSystemsSimplePath",                       # Params
       [string][ValidateSet('Chelsea',                                        # Params
                            'Salisbury',                                      # Params
                            'Millwall',                                       # Params
                            'Celtic')]$System,                                # Params
       [string]$EFile = "$Folder\e_$(get-date -format `"yyyyMMddHHmm`").txt") # Params
```

## Am I in admin mode?
```
[bool](([System.Security.Principal.WindowsIdentity]::GetCurrent()).groups -match "S-1-5-32-544")

```

## Enable remoting
```
psexec.exe \\SomePcOrServer -s powershell Enable-PSRemoting -Force

```
## AD password expiry

```
Get-ADUser -filter {name -like "*matt*"} -properties * | Select Name, PasswordLastSet,
              @{Name="PasswordExpiryDate";Expression={[datetime]::FromFileTime($_."msDS-UserPasswordExpiryTimeComputed")}},
              LockedOut, AccountExpires

```

## Add-member

```
 $R | add-member -Name SourcePath -Value $SourcePath -MemberType NoteProperty
```

## invoke-webrequest

```
Invoke-WebRequest -Uri https://raw.githubusercontent.com/mattypenny/bin/master/.matt.ps1 -OutFile c:\bin\.gmatt.ps1
```

### Names with apostrophes in(?)

```
foreach ($Name in "David Watts","Baba O'Reilly") {

    $Name =3D $Name -replace "'","''"

    $String =3D @"
    get-ADUser -filter {Name -eq '$Name'}
"@

    Invoke-Expression -Command $String | select name

}
```

### Export-Excel

```
$Whatever | export-excel -TitleBold -AutoSize -FreezeTopRow -AutoFilter -Pa=
th temp/Topics.xlsx

```

### Open files

```
openfiles /query /s BigOldServer01  /fo csv | ConvertFrom-Csv | ? 'Open Fil=
e (Path\executable)' -like "*Smokey*"

```


### Diskspace

```
Get-WMIObject Win32_LogicalDisk -filter "DriveType=3D3" -computer . |
    Select SystemName,
        DeviceID,
        @{Name=3D"%used";Expression=3D{"{0:N1}" -f((($_.size - $_.freespace=
)/$_.size) * 100)}},
        @{Name=3D"size(GB)";Expression=3D{"{0:N1}" -f($_.size/1gb)}},
        @{Name=3D"used(GB)";Expression=3D{"{0:N1}" -f(($_.size - $_.freespa=
ce)/1gb)}},
        @{Name=3D"free(GB)";Expression=3D{"{0:N1}" -f($_.freespace/1gb)}}

```

### Get-ChildItem remix (dirx)

```
get-childitem $Path
                select lastwritetime,
                                @{Label =3D "T"; Expression =3D {$_.mode.Su=
bstring(0,1).replace('-','f')  }   },
        @{Label =3D "SzGb"; Expression =3D {[math]::Round($_.length / 1Gb,1=
 ) }},
        fullname

```

### Count occurences of string in the files within a folder

```

select-string temp $(get-childitem -File) | group-object filename | select =
count, name
```

### Looking for log

```
$Logs =3D dir -recurse \\server1\c$\Oracle -include *.log                  =
      # logfiles
$RecentLogs =3D $Logs | where-object lastwritetime -gt $(get-date).adddays(=
-1)   # logfiles
```

### Manipulating a property object in a where clause

..... or something like that

```
$Teams | where-object {$_.Status.trim() -eq 'Winners'}

```

### Set up AD as a PSDrive (I think)

```
Import-Module activedirectory
New-PSDrive `
    -Name Chelsea `
    -PSProvider ActiveDirectory `
    -Server "chelseafc.co.uk" `
    -Credential (Get-Credential "matt") `
    -Root "//Rootietoot/" `
    -Scope Global

```
