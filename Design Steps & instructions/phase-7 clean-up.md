# Welcome to the last Phase-7 Clean-up

In this final Phaase we are going to clean up the account and remove all resources created during this lesson.  

# Phase 7A - Delete Load Balancer & Target Groups

First, got to the EC2 console and click on `Load Balancers` under `Load Balancing` 
Select the `SundayWORDPRESSALB`  click `Actions` and select `Delete` and then click `yes, Delete`  
Click `Target Groups`, select the target group you created earlier and click `Actions` and then `Delete`, then click `Yes, Delete`  

# Phase 7B - Delete Auto Scaling Group

Click `Auto Scaling Groups` under `Auto Scaling`  
Select the `SundayWORDPRESSASG` you created earlier and click `Delete`, type `delete` and click `delete` to confirm.  
this will take a while because the EC2 instances will be terminated as part of this.  

# Phase 7C - Delete the EFS File system

Click `Services` and type `EFS` then move to the EFS console.  
Select the `Sunday-WORDPRESS-CONTENT` File system, and click `Delete`. Confirm by typing in the file system ID and then clicking `Confirm`  

# Phase 7D - Delete RDS

Click on `Services`, type `RDS` and move to the RDS Console.  
Click `Databases`  
Select `Sundaywordpress`, click `Actions` and `Delete`  
Uncheck `Create Final Snapshot`, check the `Acknowledge` box, type `delete me` and click `Delete`  
Select `Sundaywordpress-aurora-reader2`, click `Actions` then `Delete`. Type `delete me` and click `Delete`  
Select `Sundaywordpress-aurora-reader1`, click `Actions` then `Delete`. Type `delete me` and click `Delete`  
Select `Sundaywordpress-aurora`, click `Actions` then `Delete`. unckeck `create final snapshot`, check the `Acknowledge` box, Type `delete me` and click `Delete`  

# Phase 7E - Check Progress

Check that EFS and all EC2 instances, and ASG have finished deleting - hold here until they have both been removed.

# Phase 7F - Delete Launch Template

Move to EC2 Console, click on `Launch Templates`  
Select the `Wordpress` Template, Click `Actions` and then `Delete Template`. Type `Delete` and Click `Delete`  

# Phase 7G - Wait for RDS

Move to the RDS console, click on `Databases` & wait for all RDS instances to be removed before continuing.  

# Phase 7H - RDS Snapshot

Click `Snapshots`  
Select the `Sundaywordpressmigration` snapshot, click `Actions` `Delete Snapshot`, click `Delete` to confirm.  

# Phase 7I - Cloud Formation

Move to the cloudformation console.  
Select the `myVPC` stack, click `Delete` and confirm by clicking on `Delete Stack`

# Phase 7 - CLEAN-UP FINISHED

Thats all the things ... thanks for reading ☺ 👍 
