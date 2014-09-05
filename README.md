RestoreGene
===========
This is a simple two-script framework, one T-SQL and one PowerShell (PoSh), for automating SQL database restores. You can use them to automate the generation of database restore scripts that will perform a variety of restore operations, such as a full recovery, a restore to a point in time, or stepping through a recovery sequence in STANDBY mode, using different STOPAT times. It will also generate the DBCC CHECKDB command to execute against the restored database to verify backup file integrity although a drop database command is not included. In addition, the scripts can help to initialize database mirroring, replication and availability groups or implement a basic lightweight log shipping solution

No changes should be necessary to either the PoSh script or the Stored Procedure. Just create them, and then call them, passing parameters to suit your purpose. The T-SQL script can be invoked from query analyser, copy the TSQL column in the results it returns then paste and run. The PoSh drive script can be invoked from a SQL Agent job step, an SSIS package or any PoSh shell.



The stored procedure generates the required restore scripts, including the DBCC CHECKDB command, if required. It is a useful tool by itself; in disaster recovery situations, it can construct a restore script to a given point in time, in seconds. Running it without any parameters generates a restore script for all user databases on an instance to the most recent point in time possible.

sp_RestoreGene Parameters

Parameter - Example - Description

@Database - ‘workspace’ - Restore a specific database. Defaults to all user databases

@TargetDatabase - ‘workspace_copy1′ - Allows override of restored database name, only possible if working with a specific 
database, defaults to actual database name

@WithMoveDataFiles - ‘x:\datafilesNew\’ - Overrides WITH MOVE for data file folder, defaults to the actual data file paths

@WithMoveLogFile - ‘y:\logfilesNew\’ - Overrides WITH MOVE for log file folder, defaults to the actual log file path on the assumption the standby matches the primary.

@FromFileFullUNC - ‘\\backups\full\’ - UNC path to full backup file, defaults to actual drive and folder

@FromFileDiffUNC - ‘\\backups\diff\’ - UNC path to differential backup file, defaults to actual drive and folder

@FromFileLogUNC - ‘\\backups\log\’ - UNC path to log backup files, defaults to actual drive and folder

@StopAt - ’02 Jun 2014 17:51:21:623′ - Stop at a specific date time, defaults to current (datetime2)

@StandbyMode - ‘0’ or ‘1’ - Leave database in Standby (1) or No Recovery (0), defaults to 0.

@IncludeSystemDBs - ‘0’ or ‘1’ - Iinclude restore commands for system databases? Defaults to 0 = No

@WithRecovery - ‘0’ or ‘1’ - Include WITH RECOVERY, defaults to 0 = No

@WithCHECKDB - ‘0’ or ‘1’ - Include CHECKDB, defaults to No, only possible in conjunction WithRecovery = Yes

@WithReplace - ‘0’ or ‘1’ - If WITH REPLACE is required, such as when no tail log backup exists. Defaults to No

@LogReference - ‘Incident XYZ’ - Recorded in SQL Error Log. Additional messages are logged if the restore operation fails

@LogShippingStartTime - PoSh Script Parameter - Used by to skip subsequent FULL & DIFF backups when log shipping

@LogShippingVariableDeclare - PoSh Script Parameter - Used to suppress DECLARE @MSG in result set

@LogShippingLastLSN - PoSh Script Parameter - Used to filter results sent, exclude log backups already restored







The companion PowerShell driver script is to automate the database restore process. It calls the stored procedure on the primary, to generate the required script, then it runs the restore commands on a standby server. It will perform the tasks using the existing backup history in the primary msdb.dbo.backupset table to identify the backup files required. The PoSh script can only process one database at a time, database name is a required parameter.

ps_RestoreGene Parameters


Parameter Example Description 

$PrimaryServer PBV001 Required – Primary server name 

$StandbyServer PBV002 Required – Standby server name 

$DBName AdventureWorks2012 Required – Database Name 

$FromFileFullUNC \\PBV001\`$SQLBackups\ Required – UNC Path to backup folder 

$FromFileDiffUNC   Optional – Defaults to $FromFileFullUNC 

$FromFileLogUNC   Optional – Defaults to $FromFileFullUNC 

$RestoreLog X:\SQLBackups\Log1.xml Required – XML log file path and name 

$LogShippingInitialize 0 or 1 Incremental log backups only = No (0) or Restart from full (1) = Yes. Defaults to 1 

$KillConnections 0 or 1 Kill blocking users (1) or end restore (0). Defaults to 1 

$ConsoleFeedback 0 or 1 1 by default, set this to 0 for SQL Agent jobs. 



