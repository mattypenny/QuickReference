---
title: "old(er) stuff"
date: "2012-12-25"
draft: false
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.


## perl
run_bat_sql
````

$ORACLE_SID=substr($CONNECT_STRING, index($CONNECT_STRING, "@") +1);

sleep $time_slice;

$BASENAME=~s/.bat//;
if (substr($0, 1, 1) eq ":")
{
 $USAGE= "usage: $0 bat_file db_alias...";
 $BAT_FILE=shift || die $USAGE;

if ($rc != "0" or $no_errors != 0 )
{
 if ($MAIL_SUBJECT eq "null")
 open (MYFILE, $OUT_FILE) ||
      die "Could not open $OUT_FILEn";
 while ()
 {
   if (/$error_string/)
   {
     $no_strings++;

@pslist_args=@ARGV;

open (NEWFILE, $newfile) || die "cant open $newfile" ;
while ()
{
  chomp;
  @words=split;

  if ($tids_now eq 1)
  {
        if ($words[0] eq $tid)
        {

}

@display_time=split(/:/, $arg1);
$time_in_ms=@display_time[0] * 60 * 60 * 1000 + @display_time[1] * 60 * 1000 + @display_time[2] * 1000 + @display_time[3] ;
return $time_in_ms;
}

$counter ;
$ending_value = 10 ;
@array=(1,2,3,1000);
for($counter = 0 ; $counter &lt; $ending_value ; counter++)
{
print &quot;$counter &quot; ;
print $array[$counter];
}
````
## perl debug
Perl d xxx.pl

Key commands are:

s - step (through code)

p $var - print contents of a variable

r - come out of subroutine (handy
since by default you go into all the library modules)

h - help gives the stuff below

b - set a break point

c - continue until you hit the

breakpoint

l 240.250  show lines 240 to 250

### Sybase
````
sp__newusr username, password, "real name"

use Excalibur
sp_helpuser

sp_password "sa password", xxxxx , username

sp_helpdb
sp__helpdb

Free space for each db
1> sp__alldbspace
Free space report for all Databases on server POL01 :-
Name        data_mb  data_used data_pct log_mb log_used
----------- -------  --------- -------- ------ -------
ARCHtempdb  700.00    1.17     0.17     0.00    0.00     0.00
Excalibur   9300.00   6664.16  71.66    2177.00 0.09     0.00

Devices for each db
sp__helpdbdev
Database Name   Device Name     Size         Usage
--------------- --------------- ----------- ---------------
ARCHtempdb      ARCHtempdb_data  500.000000 data and log
ARCHtempdb      ARCHtempdb_log0  200.000000 data and log
Excalibur       excalibur_data0  100.000000 data only

Location of each device
sp__helpdevice

Device Name         Physical Name
size     alloc    free
dbccdb_data01
f:\sybase\dev01\dbcc\db_data01    200.0MB
150.0MB   50.0MB

1> disk resize name='master', size='5M'
2> go
1> alter database master on master=50   (?)
2> go

To turn on debugging:
1> sp_dboption ImportData, "ddl in tran", true
2> go
Database option 'ddl in tran' turned ON for database 'ImportData'.
Running CHECKPOINT on database 'ImportData' for option 'ddl in tran' to take
effect.
(return status = 0)
1> checkpoint ImportData
2> go
````
## BJS
````
bjc show_dbjobs job_name=SERVER14 Backup Cleardown format=detail

c:\utils\strings c:\bjs\ctrl\datasched.bak | findstr DBS BAS

mkdir \\server15\D$\BJS\BJSJBDIR\matt\user

set JOBDIR_PATH= \\server1\D$\BJS\BJSJBDIR\matt\user

bjc SCHED_DBJOB JOB_NAME=SERVER14 Mon Empirix DB1 submit_now

cd p:\bjs\bjs\work

p:

bjc show_jobs JOB_TYPE=R

bjc show_log JOB_NO=2977

dir l:\bjs\bjswork*.log /od | tail

tail -f l:\bjs\bjswork\DBSx.LOG

BJS variables:
BJS_JOB_HOUR
BJS_JOB_MINUTE
BJS_JOB_MONTH
BJS_JOB_DAY_OF_MONTH
BJS_JOB_DAY_OF_WEEK
BJS_JOB_DAY_OF_YEAR
````
