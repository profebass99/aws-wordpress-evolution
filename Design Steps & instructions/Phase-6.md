
# Welcome to Phase-6



In Phase 6 you can optionally move to a 3AZ aurora cluster to provide high levels of resilience for the wordpress application. This is entirely optional and will **NOT** be covered under the free tier. or proceed directly to clean-up/tidy up

# Phase 6A - SNAPSHOT THE RDS INSTANCE

Move to the RDS Console, and databases https://console.aws.amazon.com/rds/home?region=us-east-1#databases:  
select the `Sundaywordpress` RDS instance  
Click `Action` and then `Take Snapshot`  
for `Snapshot Name` call it `SundayWORDPRESSMIGRATION`  
Click `Take Snapshot`  

Wait for the status of the snapshot to change from `creating` to `available`, this might take a few minutes  

# Phase 6B - PROVISION AURORA

Select the snapshot you just created by clicking the checkbox.  
Click `Actions` and then `Migrate Snapshot`  
for `Migrate to DB Engine` ... select `aurora`  
Leave `DB Engine Version` as default  
for `DB Instance Class` select `db.t2.small` (production may use bigger than this, but this is just illustrating the architecture)  
for `DB Instance Identifier` enter `Sundaywordpress-aurora`  or your preferred name.
for `Virtual Private Cloud (VPC)` select `Sunday-VPC`  
for `Subnet group` select `wordpressrdssubnetgroup`  
for `Availability zone` select `No prefeference`  
for `VPC security groups` select `Choose existing VPC security groups`
Remove `Default (VPC)` and select `Sunday-VPC-SGDatabase` (you might finsd random numbers and letters, thats fine)  
Ensure for `Encryption` its set to `Disable encryption`  
CLick `Migrate`  

This will take some time to complete ... the cluster and reader will need to both show as `Available` before you continue.

# Phase 6C - CREATE ADDITIONAL READERS

To improve the resiliency of the solution we can create additional replicas. To do that select the `a4lwordpress-aurora-cluster` Aurora cluster  
Click `Actions` and then click `Add Reader`  
For instance specifications select the `db.t2.small` instance class  
Under `networking and security` set `Availability zone` to `No Preference` and `No` for publicly accessible. 
Set `Disable Encryption` under `Encryption`  
Scroll down to `Settings` and under `DB instance Identifier` enter `Sundaywordpress-aurora-reader1`  
Leave everything else as default, scroll down and click `Add Reader`  

Next add another reader.. for 3AZ resilience  
Click `Actions` and then click `Add Reader`  
For instance specifications select the `db.t2.small` instance class  
Under `networking and security` set `Availability zone` to `No Preference` and `No` for publicly accessible. 
Set `Disable Encryption` under `Encryption`  
Scroll down to `Settings` and under `DB instance Identifier` enter `Sundaywordpress-aurora-reader2`  
Leave everything else as default, scroll down and click `Add Reader`  

At this point you have a full 3AZ resilient database cluster  

# Phase 6D - RECREATE THE PARAMETER for DBENDPOINT

To change the application to use this click the `a4lwordpress-aurora-cluster` Aurora Cluster  
Scroll down and locate the `Endpoints` section, and copy down the `endpoint name` for the `Writer` Endpoint inyour your clipboard  
Move to the systems manager console  
Click Parameter Store
Delete the `Sunday/Wordpress/DBEndpoint` parameter, click `Delete`, confirm by clicking `Delete Parameters`  
Click `Create Parameter`  
Under `Name` enter `/Sunday/Wordpress/DBEndpoint`  
Under `Descripton` enter `Wordpress Endpoint Name`  
Under `Tier` select `Standard`    
Under `Type` select `String`  
Under `Data Type` select `text`  
Under `Value` enter the Aurora endpoint endpoint you just copied  
Click `Create Parameter`   

# Phase 6E - REFRESH THE ASG

move to the auto scaling group
click `start instance refresh`
set minimum healthy to `0%`
click `start instance refresh`
Wait for it to provision the new instance
test it by opening new instance

This is a good way to do a rolling refresh of instances in an ASG.  

# Phase 6 - FINISHED  

This is the end of the architecture evolution, 

**NOW PROCEED TO CLEAN-UP** [Phase-7](https://github.com/profebass99/aws-wordpress-evolution/blob/main/Design%20Steps%20%26%20instructions/phase-7%20clean-up.md)
