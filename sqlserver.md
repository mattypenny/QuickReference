---
title: "sqlserver"
draft: false
date: "2012-12-25"
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.

## SqlServer Data Dictionary

### Files 
```` 
select d.name,
       f.filename
from sysaltfiles f
inner join sysdatabases d
  on (f.dbid = d.dbid)
order by 1,2
```` 

### Dts packages 
```` 
CREATE TABLE #dts_packages
(name VARCHAR(1000),
id UNIQUEIDENTIFIER,
versionid UNIQUEIDENTIFIER,
description VARCHAR(1000) NULL,
createdate DATETIME,
owner VARCHAR(100),
size INT,
packagedata IMAGE NULL,
isowner VARCHAR(100),
packagetype INT
)
INSERT INTO #dts_packages EXEC msdb.dbo.sp_enum_dtspackages

select name + ',' +  description  from #dts_packages order by name

select name,  description  from #dts_packages order by name
```` 

### Sql that SSMS Activity Monitor executes 
```` 
SELECT
   [Session ID]    = s.session_id,
   [User Process]  = CONVERT(CHAR(1), s.is_user_process),
   [Login]         = s.login_name,
   [Database]      = ISNULL(db_name(p.dbid), N''),
   [Task State]    = ISNULL(t.task_state, N''),
   [Command]       = ISNULL(r.command, N''),
   [Application]   = ISNULL(s.program_name, N''),
   [Wait Time (ms)]     = ISNULL(w.wait_duration_ms, 0),
   [Wait Type]     = ISNULL(w.wait_type, N''),
   [Wait Resource] = ISNULL(w.resource_description, N''),
   [Blocked By]    = ISNULL(CONVERT (varchar, w.blocking_session_id), ''),
   [Head Blocker]  =
   CASE
     -- session has an active request, is blocked, but is blocking others or session is idle but has an open tran and is blocking others
     WHEN r2.session_id IS NOT NULL AND (r.blocking_session_id = 0 OR r.session_id IS NULL) THEN '1'
            -- session is either not blocking someone, or is blocking someone but is blocked by another party
     ELSE ''
   END,
   [Total CPU (ms)] = s.cpu_time,
   [Total Physical I/O (MB)]   = (s.reads + s.writes) * 8 / 1024,
   [Memory Use (KB)]  = s.memory_usage * 8192 / 1024,
   [Open Transactions] = ISNULL(r.open_transaction_count,0),
   [Login Time]    = s.login_time,
   [Last Request Start Time] = s.last_request_start_time,
   [Host Name]     = ISNULL(s.host_name, N''),
   [Net Address]   = ISNULL(c.client_net_address, N''),
   [Execution Context ID] = ISNULL(t.exec_context_id, 0),
   [Request ID] = ISNULL(r.request_id, 0),
   [Workload Group] = N''
FROM sys.dm_exec_sessions s LEFT OUTER JOIN sys.dm_exec_connections c ON (s.session_id = c.session_id)
LEFT OUTER JOIN sys.dm_exec_requests r ON (s.session_id = r.session_id)
LEFT OUTER JOIN sys.dm_os_tasks t ON (r.session_id = t.session_id AND r.request_id = t.request_id)
LEFT OUTER JOIN
(
    -- In some cases (e.g. parallel queries, also waiting for a worker), one thread can be flagged as
    -- waiting for several different threads.  This will cause that thread to show up in multiple rows
    -- in our grid, which we don't want.  Use ROW_NUMBER to select the longest wait for each thread,
    -- and use it as representative of the other wait relationships this thread is involved in.
    SELECT *, ROW_NUMBER() OVER (PARTITION BY waiting_task_address ORDER BY wait_duration_ms DESC) AS row_num
    FROM sys.dm_os_waiting_tasks
) w ON (t.task_address = w.waiting_task_address) AND w.row_num = 1
LEFT OUTER JOIN sys.dm_exec_requests r2 ON (s.session_id = r2.blocking_session_id)
LEFT OUTER JOIN sys.sysprocesses p ON (s.session_id = p.spid)
ORDER BY s.session_id;

```` 

### Indexes 
```` 
CREATE TABLE #indexes
( index_name sysname,
  index_description varchar(210),
  index_keys nvarchar(2078) )

INSERT INTO #indexes EXEC sp_helpindex 'Table_name'

select index_name, index_keys
from #indexes

```` 

or....

```` 
drop table #indexes
CREATE TABLE #indexes
( index_name sysname,
  index_description varchar(210),
  index_keys nvarchar(2078) )

drop table #t_indexes
CREATE TABLE #t_indexes
( table_name varchar(100),
  index_name sysname,
  index_description varchar(210),
  index_keys nvarchar(2078) )

declare @t_name varchar(100)
set @t_name = 'Person'
INSERT INTO #indexes EXEC sp_helpindex @t_name
insert into #t_indexes
select @t_name,
       index_name,
       index_description,
       index_keys
from #indexes
delete from #indexes
select table_name, index_name, index_keys
from #t_indexes
```` 

### sql and explain plan 
```` 
select st.text, query_plan
from sys.dm_exec_query_stats as qs
cross apply sys.dm_exec_sql_text (qs.sql_handle) as st
cross apply sys.dm_exec_text_query_plan(qs.plan_handle, default, default) as qp
where st.text like N'%col_name=58%'
```` 

### Index usage 
```` 
SELECT   OBJECT_NAME(S.[OBJECT_ID]) AS [OBJECT NAME],
         I.[NAME] AS [INDEX NAME],
         USER_SEEKS,
         USER_SCANS,
         USER_LOOKUPS,
         USER_UPDATES
FROM     SYS.DM_DB_INDEX_USAGE_STATS AS S
         INNER JOIN SYS.INDEXES AS I
           ON I.[OBJECT_ID] = S.[OBJECT_ID]
              AND I.INDEX_ID = S.INDEX_ID
WHERE    OBJECTPROPERTY(S.[OBJECT_ID],'IsUserTable') = 1
AND      OBJECT_NAME(S.[OBJECT_ID]) = 'whatever'
```` 
from http://www.mssqltips.com/sqlservertip/1239/how-to-get-index-usage-information-in-sql-server/

### Table info 
```` 
sp_spaceused 'my_table'
```` 




Some stuff I find easiest to remember and/or lookup as a <i>translation from Oracle to Sqlserver</i>


### ctas

Oracle:
````
create table tax_dodgers_test
as
select * from tax_dodgers;
````

Sqlserver:
````
Select *
INTO tax_dodgers_test
from tax_dodgers
````

### Alter system flush shared_pool 
````
DBCC FREEPROCCACHE;
GO
````

<!-- Does this old rubbish
show up?
 <!-- -->

### alter system flush buffer_cache;

````
DBCC DROPCLEANBUFFERS;
GO
````

### alter system checkpoint
Just:
````
checkpoint
go
````

### Show executing sql
````
DECLARE @my_spid INT

DECLARE @cur_sysprocesses CURSOR

SET @cur_sysprocesses = CURSOR FOR
  SELECT spid from sysprocesses

OPEN @cur_sysprocesses

FETCH NEXT FROM @cur_sysprocesses INTO @my_spid

WHILE @@FETCH_STATUS = 0
  BEGIN
    PRINT convert(char, @my_spid)
    dbcc inputbuffer(@my_spid);
    FETCH NEXT FROM @cur_sysprocesses INTO @my_spid
  END

CLOSE @cur_sysprocesses

DEALLOCATE @cur_sysprocesses

GO
````
### Minus
They say 'minus' we say 'except'. Or vice versa.

Oracle:
````
select * from search_comapnies
minus
select * from search_companies_which pay_fair_taxes
````

Sqlserver:
````
select * from search_comapnies
except
select * from search_companies_which pay_fair_taxes
````

### Sql*ldr
Possibly more analogous to the external table thing, but achieves the same
````
BULK INSERT companies_to_boycott
FROM 'd:tempcompanies_that_dont_pay_fair_taxes.csv'
WITH
(
  FIELDTERMINATOR = ',',
  ROWTERMINATOR = 'n'
)
GO
````

### 1521
````
1433
````


### Show startup time
Oracle query
````
select vd.name, trim(to_char(min(logon_time), ' day DD-MON HH24:MI')) from v$session,
v$database vd
group by vd.name
/
````

Sqlserver
````
SELECT sqlserver_start_time
FROM sys.dm_os_sys_info
````
or
````
SELECT create_date
FROM sys.databases
WHERE database_id = 2 -- (TempDb)
````


### instr

````
SELECT PATINDEX('%£_m%', 'Google pays just £6m UK tax'); 
````

### substr
````
select substring ('£6m to the exchequer in 2011 on UK turnover of £395m. ' , 1, 3)
````

### ||

````
select 'the web giant contributed just £6m to the exchequer in 2011' + ' on UK turnover of £395m.'
````
Text from: <a href="http://www.telegraph.co.uk/technology/google/9460950/Google-pays-just-6m-UK-tax.html">Google pays just £6m UK tax - Daily Telegraph</a>


### wipe statistical slate for performance testing
````
CHECKPOINT;
GO
DBCC DROPCLEANBUFFERS;
GO
DBCC FREEPROCCACHE;
GO

SET STATISTICS IO ON;
SET STATISTICS TIME ON;
GO
````
### substring and convert
````
SELECT substring( convert (varchar(20), login_time, 112),5,4) + '-' +
        substring(convert(varchar(20), login_time, 14),1,5)
FROM master ..sysprocesses
WHERE spid = 1
````
### xp_cmdshell
````
set @syscommand = 'echo '+ @command_to_run +
                                '> c:tempcommand_to_run.sql'
exec xp_cmdshell @syscommand
````
### Setting database to single user and back to multi user
````
use master
ALTER DATABASE adventureworks SET SINGLE_USER WITH ROLLBACK IMMEDIATE
ALTER DATABASE adventureworks SET MULTI_USER
````

### for loop
```
declare @vdateformat int = -5
WHILE @vdateformat < 200
BEGIN
    begin try
	SET @vdateformat = @vdateformat + 1 
    SELECT '| ' + convert(varchar(3), @vdateformat) + ' | ' + CONVERT(nVARCHAR(20), GETDATE(), @vdateformat) + ' |'
    SET @vdateformat = @vdateformat + 1   
	end try
	begin catch

	end catch
END
```

### date formats
```
SELECT  CONVERT(nVARCHAR(20), GETDATE(), 130)     # see below for list of formats
```
```
| 1 | 09/05/16 |
| 2 | 16.09.05 |
| 3 | 05/09/16 |
| 4 | 05.09.16 |
| 5 | 05-09-16 |
| 6 | 05 Sep 16 |
| 7 | Sep 05, 16 |
| 8 | 11:16:39 |
| 9 | Sep  5 2016 11:16:39 |
| 10 | 09-05-16 |
| 11 | 16/09/05 |
| 12 | 160905 |
| 13 | 05 Sep 2016 11:16:39 |
| 14 | 11:16:39:493 |
| 20 | 2016-09-05 11:16:39 |
| 21 | 2016-09-05 11:16:39. |
| 22 | 09/05/16 11:16:39 AM |
| 23 | 2016-09-05 |
| 24 | 11:16:39 |
| 25 | 2016-09-05 11:16:39. |
| 100 | Sep  5 2016 11:16AM |
| 101 | 09/05/2016 |
| 102 | 2016.09.05 |
| 103 | 05/09/2016 |
| 104 | 05.09.2016 |
| 105 | 05-09-2016 |
| 106 | 05 Sep 2016 |
| 107 | Sep 05, 2016 |
| 108 | 11:16:39 |
| 109 | Sep  5 2016 11:16:39 |
| 110 | 09-05-2016 |
| 111 | 2016/09/05 |
| 112 | 20160905 |
| 113 | 05 Sep 2016 11:16:39 |
| 114 | 11:16:39:507 |
| 120 | 2016-09-05 11:16:39 |
| 121 | 2016-09-05 11:16:39. |
| 126 | 2016-09-05T11:16:39. |
| 127 | 2016-09-05T11:16:39. |
| 130 |   3 ?? ????? 1437 11: |
| 131 |  3/12/1437 11:16:39: |
```
