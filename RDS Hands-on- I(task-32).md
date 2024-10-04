## Amazon RDS (Relational Database Service) is a cloud service provided by AWS that makes it easy to set up, operate, and scale a relational database in the cloud. In simple terms, it allows you to use databases like MySQL, PostgreSQL, or Oracle without having to worry about managing the hardware or software yourself.
#RDS handles tasks like backups, patching, and scaling, so you can focus on building your applications instead of managing your database.
  BASIC RDS SETUP:
#Step 1: Launch an RDS Instance:
 Step 2: Connect to RDS using DBeaver
 Step 3: Perform Tasks on MySQL or PostgreSQL RDS
1. Create Read Replica: 
           Read Replicas are a valuable tool for improving database performance, scalability, and availability while reducing the load on the primary database instance. They are commonly used in cloud environments to support modern application architectures.
 ##In the RDS console, select your primary database instance.
   Click on Actions and select Create read replica.
   Configure the settings for the read replica (e.g., instance class, storage type) and click on Create read replica.**
2. Enable Multi-AZ:Enabling Multi-AZ (Multi-Availability Zone) deployment for your RDS (Relational    Database Service) instance enhances availability, durability, and resilience against failures
            Select the database instance.
            Click on Modify.
            Scroll to the Availability & durability section.
            Check the box for Multi-AZ deployment.
            Review and apply the changes.
3.Manual Failover: Manual failover in RDS is a valuable tool for database administrators and application owners. It provides flexibility and control over the database environment, allowing for testing, maintenance, performance optimization, and recovery from issues. By utilizing this feature effectively, you can enhance the reliability and availability of your database services.
            In the RDS console, select your primary DB instance.
            Click on Actions and select Reboot.
            In the reboot dialog, select Reboot with failover and confirm.
4. Promote Read Replica to Master
            Select the read replica instance.
            Click on Actions and select Promote read replica.
             Confirm the action.
5. Take Snapshot
            Select the database instance you want to snapshot.
            Click on Actions and select Take snapshot.
            Enter a name for the snapshot and confirm.
6. Restore from Snapshot
             In the RDS console, navigate to Snapshots.
             Select the snapshot you want to restore.
             Click on Actions and select Restore snapshot.
             Configure the settings for the new instance and click Restore DB Instance.
7. Modify Max Connections Setting in Parameter Group
             Navigate to the Parameter groups section in RDS.
             Create a new parameter group (or select an existing one).
             Modify the max_connections parameter and set it to 300.
             Save changes.
             Apply the parameter group to your DB instance by selecting the instance, clicking Modify, and selecting the parameter group.
8. Perform Point in Time Recovery
             In the RDS console, select your database instance.
             Click on Actions and select Restore to point in time.
             Select the point in time (3 minutes before the current time) and configure the settings for the new instance.
             Click Restore DB Instance.

 