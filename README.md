RestoreGene
===========
This is a simple two-script framework, one T-SQL and one PowerShell (PoSh), for automating SQL database restores. You can use them to automate the generation of database restore scripts that will perform a variety of restore operations, such as a full recovery, a restore to a point in time, or stepping through a recovery sequence in STANDBY mode, using different STOPAT times. It will also generate the DBCC CHECKDB command to execute against the restored database to verify backup file integrity although a drop database command is not included. In addition, the scripts can help to initialize database mirroring, replication and availability groups or implement a basic lightweight log shipping solution

No changes should be necessary to either the PoSh script or the Stored Procedure. Just create them, and then call them, passing parameters to suit your purpose. The T-SQL script can be invoked from query analyser, copy the TSQL column in the results it returns then paste and run. The PoSh drive script can be invoked from a SQL Agent job step, an SSIS package or any PoSh shell.

The stored procedure generates the required restore scripts, including the DBCC CHECKDB command, if required. It is a useful tool by itself; in disaster recovery situations, it can construct a restore script to a given point in time, in seconds. Running it without any parameters generates a restore script for all user databases on an instance to the most recent point in time possible.


The companion PowerShell driver script is to automate the database restore process. It calls the stored procedure on the primary, to generate the required script, then it runs the restore commands on a standby server. It will perform the tasks using the existing backup history in the primary msdb.dbo.backupset table to identify the backup files required. The PoSh script can only process one database at a time, database name is a required parameter.

Please click here for a description of all parameters in each script and examples. - http://paulbrewer.wordpress.com/2014/08/15/restore-gene-version-3-free-public-framework-technical-article/
