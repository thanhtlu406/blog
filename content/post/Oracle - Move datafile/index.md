+++
author = "ThanhNV"
title = "Rename a datafile in Dataguard environment"
date = "2024-03-22"
description = "Provides detailed guidance on renaming or moving a datafile within an Oracle Active Data Guard environment."
tags = [
    "oracle",
]
categories = [
    "Oracle",
]
series = ["Oracle"]
+++

## ON PRIMARY:
### Check the database role:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### Switch standby file management from automatic to manual:
```
SHOW PARAMETER STANDBY_FILE_MANAGEMENT;
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### Check the location and names of files to be changed on Primary:
```
SELECT file_name FROM dba_data_files WHERE tablespace_name='HIS';
```

### Shutdown and move files on Primary:
```
SHUTDOWN IMMEDIATE;
-- Move or rename the file
ALTER TABLESPACE HIS RENAME DATAFILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
```

### Startup and open the database on Primary:
```
STARTUP MOUNT;
ALTER DATABASE OPEN;
```

## ON STANDBY:
### Check the database role:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### Check the status of applied archives and Data Guard synchronization:
```
SELECT SEQUENCE#, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
```

### Switch standby file management from automatic to manual:
```
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### Check the file locations on Standby:
```
SELECT ts#, name FROM v$tablespace WHERE name='HIS';
SELECT name FROM v$datafile WHERE ts#=6;
```

### Properly cancel recovery process and shutdown on Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
SHUTDOWN IMMEDIATE;
```

### Move files on Standby:
```
# Move or rename the file
mv /u02/oradata/orcl/example01.dbf /u03/oradata/orcl/example01_temp.dbf
```

### Start, rename data file, and open database in read-only mode on Standby:
```
STARTUP MOUNT;
ALTER DATABASE RENAME FILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
ALTER DATABASE OPEN READ ONLY;
```

### Verify new file locations on Standby:
```
SELECT name FROM v$datafile WHERE ts#=6;
```

### Begin recovery process on Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE USING CURRENT LOGFILE DISCONNECT FROM SESSION;
```

## Reset standby file management to automatic
### Reset standby file management to automatic on both Primary and Standby:
```
Primary: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
Standby: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
```