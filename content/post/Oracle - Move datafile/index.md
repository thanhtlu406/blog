+++
author = "ThanhNV"
title = "Rename a datafile in Dataguard environment"
date = "2024-03-22"
description = "Rename a datafile in Dataguard environment"
tags = [
    "oracle",
]
categories = [
    "Oracle",
]
series = ["Oracle"]
+++

## TRÊN PRIMARY:
### Kiểm tra vai trò của cơ sở dữ liệu:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### Thay đổi quản lý file standby từ tự động sang bằng tay:
```
SHOW PARAMETER STANDBY_FILE_MANAGEMENT;
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### Kiểm tra vị trí và tên file cần đổi trên Primary:
```
SELECT file_name FROM dba_data_files WHERE tablespace_name='HIS';
```

### Shutdown và move file trên Primary:
```
SHUTDOWN IMMEDIATE;
-- Move or rename the file
ALTER TABLESPACE HIS RENAME DATAFILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
```

### Startup và cập nhật control file trên Primary:
```
STARTUP MOUNT;
ALTER DATABASE OPEN;
```

## Trên STANDBY:
### Kiểm tra vai trò của cơ sở dữ liệu:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### Kiểm tra trạng thái của archive applied và Data Guard in sync:
```
SELECT SEQUENCE#, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
```

### Thay đổi quản lý file standby từ tự động sang bằng tay:
```
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### Kiểm tra vị trí file trên Standby:
```
SELECT ts#, name FROM v$tablespace WHERE name='HIS';
SELECT name FROM v$datafile WHERE ts#=6;
```

### Hủy bỏ quá trình recover và shutdown đúng cách trên Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
SHUTDOWN IMMEDIATE;
```

### Move file trên Standby:
```
# Move or rename the file
mv /u02/oradata/orcl/example01.dbf /u03/oradata/orcl/example01_temp.dbf
```

### Start và cập nhật control file trên Standby:
```
STARTUP MOUNT;
ALTER DATABASE RENAME FILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
```

### Verify vị trí mới trên Standby:
```
SELECT name FROM v$datafile WHERE ts#=6;
```

### Bắt đầu quá trình recover trên Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE USING CURRENT LOGFILE DISCONNECT FROM SESSION;
```

## Đặt lại quản lý file standby thành tự động
### Đặt lại quản lý file standby thành tự động trên cả Primary và Standby:
```
Primary: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
Standby: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
```