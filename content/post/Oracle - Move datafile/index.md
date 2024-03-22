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
### 1. Kiểm tra vai trò của cơ sở dữ liệu:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### 2. Thay đổi quản lý file standby từ tự động sang bằng tay:
```
SHOW PARAMETER STANDBY_FILE_MANAGEMENT;
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### 3. Kiểm tra vị trí và tên file cần đổi trên Primary:
```
SELECT file_name FROM dba_data_files WHERE tablespace_name='HIS';
```

### 4. Shutdown và move file trên Primary:
```
SHUTDOWN IMMEDIATE;
-- Move or rename the file
ALTER TABLESPACE HIS RENAME DATAFILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
```

### 5. Startup và cập nhật control file trên Primary:
```
STARTUP MOUNT;
ALTER DATABASE OPEN;
```

## Trên STANDBY:
### 6. Kiểm tra vai trò của cơ sở dữ liệu:
```
SELECT name, db_unique_name, database_role, switchover_status FROM v$database;
```

### 7. Kiểm tra trạng thái của archive applied và Data Guard in sync:
```
SELECT SEQUENCE#, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
```

### 8. Thay đổi quản lý file standby từ tự động sang bằng tay:
```
ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=MANUAL;
```

### 9. Kiểm tra vị trí file trên Standby:
```
SELECT ts#, name FROM v$tablespace WHERE name='HIS';
SELECT name FROM v$datafile WHERE ts#=6;
```

### 10. Hủy bỏ quá trình recover và shutdown đúng cách trên Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE CANCEL;
SHUTDOWN IMMEDIATE;
```

### 11. Move file trên Standby:
```
# Move or rename the file
mv /u02/oradata/orcl/example01.dbf /u03/oradata/orcl/example01_temp.dbf
```

### 12. Start và cập nhật control file trên Standby:
```
STARTUP MOUNT;
ALTER DATABASE RENAME FILE '/u02/data/his/oradata/HIS100.dbf' TO '/data/his/oradata/HIS100.dbf';
```

### 13. Verify vị trí mới trên Standby:
```
SELECT name FROM v$datafile WHERE ts#=6;
```

### 14. Bắt đầu quá trình recover trên Standby:
```
ALTER DATABASE RECOVER MANAGED STANDBY DATABASE USING CURRENT LOGFILE DISCONNECT FROM SESSION;
```

## Đặt lại quản lý file standby thành tự động
### 15. Đặt lại quản lý file standby thành tự động trên cả Primary và Standby:
```
Primary: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
Standby: ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
```