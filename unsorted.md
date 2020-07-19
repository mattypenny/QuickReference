---
title: "unsorted 'stuff'"
date: "2012-12-25"
draft: false
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.


This Markdown file is for dumping un-sorted bits of code

```

get-adgroup -Filter {name -like "*"} | select name, groupcategory, samaccountname | sort-object -property name
get-aduser -Filter {name -like "*sql*"} | select name,  samaccountname, objectclass, Enabled

schtasks /Run /S server1 /TN "run some stuff"
schtasks /CHANGE /TN "\_run Install-UpdatesIfItIsPatchDay" /ENABLE /S Server1

switch ($Day){ 'mon' {"Monday"} ; 'tue' {"Tuesday"} }

Set-ADAccountPassword -Identity matt -NewPassword (ConvertTo-SecureString -AsPlainText "qwert@12345" -Force) -reset
Set-ADAccountPassword -Identity matt -NewPassword $(Read-Host -Prompt "Enter password" -AsSecureString)  -reset

Import-Module activedirectory
New-PSDrive `
    -Name Chelsea `
    -PSProvider ActiveDirectory `
    -Server "chelseafc.co.uk" `
    -Credential (Get-Credential "matt") `
    -Root "//Rootietoot/" `
    -Scope Global


                         
$Username = 'matt'
$StartTime = $(get-date).Addhours(-1)
$xx = Invoke-Command -ComputerName domaincontroller1 {
 
    Get-WinEvent -FilterHashtable @{LogName='Security';StartTime=$Using:StartTime}
  
} 
```

### Scheduled tasks
```
convertfrom-csv $(schtasks /Query /S server1  /V /FO CSV) | ? Taskname -ne 'taskname' | ? 'Next Run Time' -ne 'N/A' | ? Author -notlike 'Microsoft*' | ? taskname -ne 'Enabled' | select taskname, 'Next Run Time', 'Run As User', Author, 'Task To Run' | ft -AutoSize

```

### Moving a database file - madf

```
alter database _dba_full_recovery modify file (name = '_dba_full_recovery_log', filename = 't:\sql_data\inst1\_dba_full_recovery_log.ldf') # madf
ALTER DATABASE _dba_full_recovery SET OFFLINE WITH ROLLBACK IMMEDIATE                                                                      # madf
Move-Item \\serv1\d$\sql_data\inst1\_dba_full_recovery_log.ldf \\serv1\t$\sql_data\inst1\_dba_full_recovery_log.ldf                        # madf
ALTER DATABASE _dba_full_recovery SET ONLINE                                                                                               # madf

```

### ReportingServicesTools (SSRS)

```
$x = foreach ($R in Get-RsFolderContent -ReportServerUri http://server1/ssrs1 -Path "/Data Sources") {
    [string]$Path = $R.Path
    $D = Get-RsDataSource -ReportServerUri http://server1/ssrs1 -Path  $path 
    [PSCustomObject]@{ Path = $Path
        Extension = $D.Extension 
        ConnectString = $D.ConnectString 
        Username = $D.UserName  
    }   
} # ReportingServicesTools (SSRS) 

```

### $error
```
$Error[0] | 
    select -ExpandProperty InvocationInfo |                                                                                # $Error[0]
    gm |                                                                                                                   # $Error[0]
    ? membertype -eq 'Property' |                                                                                          # $Error[0]
    select @{L='x'                                                                                                         # $Error[0]
             E = {'[string]$' + $_.name + ' = $I.' + $_.name + ' ; write-debug "`$' + $_.name + ': <$' + $_.name + '>"'}}  # $Error[0]

```

### Add user to local administrators group

```
net localgroup administrators /add domain\matty

```

### Acl's

```
get-acl '\\SomeServer\C$\temp\' | select -ExpandProperty Access | select filesystemrights, identityreference
```

### Validate a string as a date

```
[datetime]::ParseExact("19991231","yyyyMMdd",$null)

```

### Fix the bloated logfile

```
invoke-sqlcmd -ServerInstance $ServerInstance -query 'alter database wslogdb70_${nn} set recovery full'                       # BloatedLogfile
invoke-sqlcmd -ServerInstance $ServerInstance -query 'alter database wslogdb70_${nn} set recovery simple'                     # BloatedLogfile
invoke-sqlcmd -ServerInstance $ServerInstance -query "USE [wslogdb70_${nn}]; DBCC SHRINKFILE (N'wslogdb70_${nn}_log' , 500)"  # BloatedLogfile
```

### Pester passthru

```
invoke-pester SqlLastDatabaseBackup.tests.ps1  -passthru | select -ExpandProperty TestResult | select Name, Result | ? Result -ne 'Passed' 
```

### Get cmdlets to inherit $DebugPreference 

```
$DebugPreference = $PSCmdlet.GetVariableValue('DebugPreference')  

```

### AD stuff

```
Get-ADTrust -filter * | select direction, name
Get-ADDomainController -DomainName domain.co.uk -Discover

```

### Show open files

```
Openfiles.exe /query /s Server

```


### Show commit messages for a particular file

```
git log --follow -- .\data\DiskThresholds.csv
```

### Eventlog (quicker)

```
Get-EventLog -ComputerName server1 -LogName system | select -first 25 | Sort-Object -Property timewritten

```
