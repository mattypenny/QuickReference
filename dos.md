---
title: "dos (cmd)"
date: "2012-12-25"
draft: false
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.


##### Print to a printer from DOS

````
print /D:"\prt01SVPRT006" new.sql
````

##### Copying files that are newer or don't exist at destination

````
xcopy source_location destination_location /D
````

##### Run script on several Oracle databases
````

for %I in (db1 db2 db3 db4) do sqlplus mattyp/pw@%I @suptimex
````

##### Dos equivalent of unix find

````
dir e:\autobackup /S /B | findstr TRN
````

##### Dos If, goto
````
set dbg=D:\perl\bin\perl c:\utils\dbg.pl

%dbg% Script beginning

set SERVER=%1
if "%BJS_JOB_DAY_OF_WEEK%" == "1" set DAY_OF_WEEK=Sun

rem Set current date for use at the end
for /f "tokens=1,2" %%I in ('date /t') do set my_date=%%J

if  "%DB_OR_NODB%" == "nodb" goto FULL_OR_INCREMENTAL
if "%SERVER%" == " server1 " cmd /k ps_Oracle.bat %SERVER% d:perlbinperl ....

:FULL_OR_INCREMENTAL
%dbg% Do full or incremental stuff

````
##### Show number of recent backups (I think!)
````
for /f %%I in ('bplist -s %my_date% -I  -R -l -C %SERVER% -nt_files -S server1 %FILE_STRING%') do set /a LINE_COUNT+=1
echo %LINE_COUNT%
````
##### To run a script across several Oracle instances:
````
@echo off
set pw="%1%"
set myscript=%2%
set myparam1=%3%
for %%I in (db1 db2 db3 db4) do ^
echo %%I &amp;&amp; ^
sqlplus -s mattyp/%pw%@%%I @%myscript% @%myparam1%
````

##### To spool the contents of the latest file matching a spec to the screen
````
for /f %%I in ('dir /B /N /S /OD %DIR_STRING% ') do set LAST_FILE=%%I
type %LAST_FILE%
````

##### Can't quite remember what this does!
````
for /f "tokens=1,2" %%I in ('dir %LAST_FILE% ^| findstr /V "Volume Directory ( ^$"')  do set DATE_CREATED=%%I %%J
````
##### To output a blank line:
````
Echo.
````

##### To copy something to a dated version of itself:
````
copy daily_check.bat daily_check.bat_%DATE:~6,4%%DATE:~3,2%%DATE:~0,2%%TIME:~0,2%%TIME:~3,2%%TIME:~6,2%
````

##### To see if you have admin privs
````
Right-click on cmd and Run as administrator and see if you get any error message.. else type in cmd prompt the following:
net localgroup administrators
then see if your username is in there..
````

##### Show services
````
psservice \server1 -u server1mattuser -p pw query Client32
````

##### Show session

````
psexec \server1 -U server1 mattuser -P password query session /server: server1
````
##### Kill session
````
psexec server1 -U server1 mattuser -P password reset session /server: server1 1
````
##### Not sure!
````
psexec server1 -U an_admin_user -P password net user /Active:Yes mattuser 
````

##### Reading line by line, even when there are spaces
Setting delims to something that's not in the file will get DOS to read the whole line into %I

````
for /F "delims=~" %I in (data.csv) do echo %I
````

....otherwise it will treat spaces as delimiting separate fields


##### No idea

````
for /F "tokens=1,2,3,4,5,6 delims=, eol=;" %I in (h:scriptsdb.txt) do echo %I %J %K %L %M %N
````
##### No idea
````
for /F "tokens=1 delims=, eol=;" %I in (H:workingdbfiles.txt) do
echo %I &gt;&gt; grep.out &amp; grep -i %I c:workingdsmsched.log &gt;&gt;
grep.out
````

##### Backing up with robocopy
For a quick test:
````
robocopy "c:\users\matt\music\Elvis Unplugged" "e:\englebert_users_robocopy\matt\music\Elvis Unplugged" /COPY:DAT /MIR /B /X /R:0
````
For a full backup:
````

robocopy "c:\users" "e:\englebert_users_robocopy" /COPY:DAT /MIR /B /XJ /R:0
attrib -S -H  "e:\englebert_users_robocopy"  /D /S
````
With a tip of the hat to <a href="http://www.geekality.net/2010/11/15/how-to-backup-a-folder-with-robocopy/">How to backup a folder with Robocopy on Geekality</a> where I looked up the options.

### whence.bat - an equivalent to the unix whence or type
````
@echo off

set TO_FIND=%1
for %%I in ("%TO_FIND%") do echo %%~$PATH:I
````
