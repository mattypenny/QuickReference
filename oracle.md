---
title: "oracle"
date: "2012-12-25"
draft: true
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.


### move database file
````
alter database rename file 'D:TOOLS01.DBF' to
'E:TOOLS01.DBF';
````
### create stats table
````

exec dbms_stats.create_stat_table('LMS', 'STATS');
````
### set event
````

alter session set events='1652 trace name errorstack level 3';
````
### create tablespace
````

CREATE TABLESPACE auto_seg_ts DATAFILE
'file_2.f' SIZE 1M
  EXTENT MANAGEMENT LOCAL
  SEGMENT SPACE MANAGEMENT manual;
````
### put message in alert log
````

exec dbms_system.ksdwrt(2, 'ORA-MUCK: Matt arsing about again');
exec dbms_system.ksdfls;
````
### start archivelogging
````

ALTER SYSTEM ARCHIVE LOG START;
````
### re-compile
````

exec utl_recomp.recomp_serial('dog_OWNER');
````
### sqlnet flags
````

# SQLNET.TRACE_LEVEL_CLIENT = USER
# SQLNET.TRACE_directory_CLIENT = C:temp
# SQLNET.TRACE_timestamp_CLIENT = ON

# TRACE_LEVEL_CLIENT = 16
# TRACE_FILE_CLIENT = client.trc
# TRACE_DIRECTORY_CLIENT = d:tempsqlnettrace
# TRACE_TIMESTAMP_CLIENT = ON
# TRACE_FILELEN_CLIENT = 1000
# TRACE_FILENO_CLIENT = 4
````

### Export

````
exp buffer=100000                       
   compress=N                          
   consistent=Y                        
   constraints=Y                       
   direct=Y                            
   feedback=1000                       
   file=${ORACLE_SID}_full_export.dmp  
   full=Y grants=Y indexes=Y           
   log=./${ORACLE_SID}_full_export.log 
   statistics=NONE                     
   userid=/
````
### Import
````
imp userid=/                            
   buffer=100000                       
   file=${ORACLE_SID}_Sun.dmp          
   ignore=Y grants=Y indexes=Y         
   rows=Y                              
   full=Y                              
   commit=Y                            
   log =imp.log 
   feedback=100
````
## Oracle text
````
create index sc_sol_textidx_desc on sc_solutions (description) indextype is ctxsys.Context;
````
````

exec ctx_ddl.sync_index ('E_OWNER.SC_SOL_TEXTIDX_DESC', '2M');
````
````

select count(*) into   v_hits_got from   e_owner.sc_solutions
where  contains(solution, p_keyword) &gt; 0;

````

## pl/sql
````
````
set serveroutput on size 900000
declare

cursor cur_objects is
select owner, object_name, object_type
from dba_objects
where owner='&amp;1';
````

### for loop
````
set serveroutput on size 900000
declare
i number;
begin
for i in 1..1000
loop
 dbms_output.put_line('Hello');
end loop;
end;
````

### create function
````
create or replace function emp_typical_sql return number as begin
declare
x number;
begin
select count(*)
into x
from dba_tab_columns;
return x;
end;
end;
/
````
### set up editor
````
define_editor='vi'
````

### using variables in sqlplus
````
variable xx number;
select 7 into :xx from dual;
select * from pcn_number
where pcn_no = :xx;
````
### Removing duplicates
````
delete from handset where rowid not in (select min(rowid) from handset group by hset_imei)
````
### create database link
````
create database link vulcan_test connect to dba_read_user
identified by pw using 'VULCAN_TEST'
````
### create index
````
create index gtx2.mtp_ca1 on gtx2.mtp (CUST_ACC_NO)
tablespace CACHE1_IDX storage (initial 50M next 50M pctincrease 1)
````
### ctas
````
create table mtpmuckabout tablespace users
as select * from databases
````
### NLS 
````
set NLS_LANG=american_america.we8pc850
(whatever given by chcp)
````

### Locking	 
````
prompt Blocking sessions:
select * from v$lock
where block = 1
/
prompt Blocking sessions and blocked sessions detail
select l1.sid, ' IS BLOCKING ', l2.*
from v$lock l1, v$lock l2
where l1.block =1 and l2.request > 0
and l1.id1=l2.id1
and l1.id2=l2.id2
/

prompt Blocking sessions and blocked sessions summary
select l1.sid, ' IS BLOCKING ', l2.sid
from v$lock l1, v$lock l2
where l1.block =1 and l2.request > 0
and l1.id1=l2.id1
and l1.id2=l2.id2
/
prompt Showing sql for blocking lock. Remember that this probably isnt
prompt the sql that's done the block because its probably moved on
select sid,
       se.username usern,
       sql_text
from v$sqlarea sq, v$session se
where se.sql_address = sq.address
and (sid = (select sid from v$lock
           where block =1 )
or   sid = (select l2.sid
            from v$lock l1, v$lock l2
            where l1.block =1 and l2.request > 0
            and l1.id1=l2.id1
            and l1.id2=l2.id2))
/

````
