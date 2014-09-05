RestoreGene
===========
This is a simple, free two-script framework, one T-SQL and one PowerShell (PoSh), for automating SQL database restores. You can use them to automate the generation of database restore scripts that will perform a variety of restore operations, such as a full recovery, a restore to a point in time, or stepping through a recovery sequence in STANDBY mode, using different STOPAT times. It will also generate the DBCC CHECKDB command to execute against the restored database to verify backup file integrity although a drop database command is not included. In addition, the scripts can help to initialize database mirroring, replication and availability groups or implement a basic lightweight log shipping solution

The stored procedure generates the required restore scripts, including the DBCC CHECKDB command, if required. It is a useful tool by itself; in disaster recovery situations, it can construct a restore script to a given point in time, in seconds. Running it without any parameters generates a restore script for all user databases on an instance to the most recent point in time possible.

The companion PowerShell driver script is to automate the database restore process. It calls the stored procedure on the primary, to generate the required script, then it runs the restore commands on a standby server. It will perform the tasks using the existing backup history in the primary msdb.dbo.backupset table to identify the backup files required. The PoSh script can only process one database at a time, database name is a required parameter.

No changes should be necessary to either the PoSh script or the Stored Procedure. Just create them, and then call them, passing parameters to suit your purpose. The T-SQL script can be invoked from query analyser, copy the TSQL column in the results it returns then paste and run. The PoSh drive script can be invoked from a SQL Agent job step, an SSIS package or any PoSh shell.

What’s new in the latest version (Version 3)
 This is an ongoing effort to provide a ‘best of breed’ free community tool; a feature rich, robust and flexible database restore framework. It started life as a simple T-SQL query that identified the backup files needed to recover to a given point in time, gradually new features were added, including:

Stored Procedure – sp_RestoreGene
•V3.4 – Remove TRY CATCH from RESTORE LOG commands
•V3.4 – Remove CTE’s, replace with 2 temporary tables
•V3.32 – Parameterize WITH REPLACE, safety check that a tail of the log backup exists
•Include CHECKSUM where possible, in the restore script
•Backward compatibility (to SQL Server 2005), compressed backup size issue
•Cater for multiple (< 11) stripped backup files
•Allow over ride of database data and log file locations
•Allow override of backup file location
•V3.32 – Add error handling and logging
•V3.32 – Allow for restored standby/target database name override
•Include a final WITH RECOVERY and optionally DBCC CHECKDB
•Include a WITH STANDBY option
•V3.5 – goestock suggestion – Include device type 7 for AWS EC2 backups
•V3.6 – Richard found and fixed a problem with variable declare where database name has a space or full stop.

PowerShell Script – ps_RestoreGene
•V3.4 – Terminates restore sequence on first failure
•V3.2 – Use ‘Last Restore LSN’ for incremental restores of new log files, for log shipping ignore new full and diff backups
•V3.32 – Additional error handling and improved (xml) logging
•‘Kill Blocking User’ option in the PoSh driver script
•V3.5 – Include option to suppress Write-Host feedback in SQL Agent jobs
•V3.6 – Query timeout setting increased and exit rather than error if no new log files to restore when in log shipping mode.

There have been a number of versions of this stored procedure and PoSh driver script over the last 18 months, this is the most recent and most stable. The tests developed and used for each version have together formed an ever expanding regression test suite, there are no bugs that I am aware of.
