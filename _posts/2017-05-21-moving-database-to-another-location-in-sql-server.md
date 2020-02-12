---
layout: post
title:  "Moving Database To Another Location in SQL Server - Lesson Learned"
author: Nemi Chand
categories: [ SQL Server ]
tags: [sqlserver,database]
image: assets/images/2017/05/SQL_server_move_database_logicalnames_alter_Command.jpg
description: "Fix: Unable to open the physical file. Operating system error (Access is denied.). In this article, we'll learn the best practice of moving database to another physical location on the Server."
featured: false
hidden: false
---

Fix: Unable to open the physical file. Operating system error (Access is denied.). In this article, we'll learn the best practice of moving database to another physical location on the Server.

## Introduction

In this article, we'll learn the best practices of moving a database to another physical location on the Server.

One of my  friends was moving the database files to another location from the default location. He suddenly ran into an error stating, "Unable to Open the physical file "xxx" . Operating system error (Access is denied.) ".

This is an unexpected error on production server. When he asked me for help, I thought it would be good to share the solution with others too so that they can avoid this problem in their application.

The general recommendation is to keep database physical files on a different location from the default location . It would be better to separate the data and log files on different logical drives. It will boost the I/O operations.

![Sql server Error]({{ site.baseurl }}/assets/images/2017/05/SQL_server_move_database.png)

Here are the steps involved in moving files onto another location.

### Access to required Security Permissions

Make sure that the new physical location of database has the required security permissions. That folder must have full control over the MSSQLSERVER service. If you do not give permission, it will throw an error `unable to open the physical file`.

### Take database to offline mode

Before starting the process, make sure that you have taken the database in offline mode. If you do not set it to offline mode, it may have data discrepancy to new database.

```sql
ALTER DATABASE CopyMoveDBTest SET OFFLINE WITH ROLLBACK IMMEDIATE;
```

### Alter the logical file path to new path

In this step, you have to modify the logical file path of .MDF , .NDF and .LDF files. Get the list of current logical names with path of database.

```sql
USE master;  
GO  
-- Return the logical file name.  
SELECT name, physical_name AS CurrentLocation, state_desc  
FROM sys.master_files  
WHERE database_id = DB_ID(N'CopyMoveDBTest');  
```

![Sql server Before]({{ site.baseurl }}/assets/images/2017/05/SQL_server_move_database_logicalnames_before.jpg)

Here is the command to alter the logical path. Make sure that the new location exists. Otherwise, it will give the "directory lookup failed" error.

```sql
USE master;  
GO  
--alter the logical name with ne  
-- for mdf file  
ALTER DATABASE CopyMoveDBTest  
MODIFY FILE ( NAME = CopyMoveDBTest,  
FILENAME = 'F:\TempDB\CopyMoveDBTest.mdf');  
--for ldf file  
ALTER DATABASE CopyMoveDBTest  
MODIFY FILE ( NAME = CopyMoveDBTest_log,  
FILENAME = 'F:\TempDB\CopyMoveDBTest_log.ldf');
```

![Sql server Command]({{ site.baseurl }}/assets/images/2017/05/SQL_server_move_database_logicalnames_alter_Command.jpg)

### Move physical files to new location

Before starting this process, make sure that you have completed the above steps . Just move the physical file using copy/paste, Robocopy, or any other option you would like.

### Verify the logical name and location

Before moving to the next step, verify the changes you have made. Make sure that the new location is correct.

```sql
USE master;  
GO  
-- Return the logical file name.  
SELECT name, physical_name AS CurrentLocation, state_desc  
FROM sys.master_files  
WHERE database_id = DB_ID(N'CopyMoveDBTest')  
```

![Sql server After]({{ site.baseurl }}/assets/images/2017/05/SQL_server_move_database_logicalnames_after.jpg)

### Bring database to Online mode

After completing the above steps, you are done with all the required changes but your database is in an offline state. So, you need to bring it back to the online mode

### Set database mode to online

```sql
USE master;  
GO  
ALTER DATABASE tempdb SET ONLINE;
```

### Summary

In this article, you have learned how to move database files to another location safely. Thanks for reading. If you have any other approach in mind, please let me know via the comments section.
