# Welcome to Phase-1

Here we will Setup the environment which WordPress will run from.
Configure some SSM Parameters which the manual and automatic stages of this advanced demo series will use and perform a manual install of wordpress and a database on the same EC2 instance.
This is the starting point .. the common wordpress configuration which you will evolve over the coming demo stages.

# Phase 1A - Login to an AWS Account

Login to an AWS account using a user with admin privileges and ensure your region is set to us-east-1 N. Virginia

Click [HERE](googl.com) to auto configure the VPC in which we will  be running wordpress

Wait for the STACK to move into the CREATE_COMPLETE state before continuing.


# Phase 1B - Create an EC2 Instance to run wordpress

Move to the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1  
Click `Launch Instance`  
Find the `Amazon Linux 2 AMI (HVM), SSD Volume Type` AMI  
Make sure  `64-bit (x86)` is selected  
Click `Select`
Select whichever instance shows as `Free tier eligible`  
Click `Next: Configure Instance Details`  
For `Network` select `Sunday-VPC`  
for `Subnet` select `sn-Pub-A`  
For `IAM role` select `Sunday-VPC-WordpressInstanceProfile`  
Check the box `Unlimited` against `Credit specification`
_Even though it says Additional Changes may apply thats only if the rolling 24 hour average exceeds baseline, it won't_  
Click `Next: Add Storage`  
Click `Next: Add Tags`  
Click `Add Tag`  
Set `Key` to `Name` & set `Value` to `Wordpress-Manual`  
Click `Next: Configure Security Group`  
Check `Select an existing security group`  
Select `Sunday-VPC-SGWordpress` it will have randomness after it, thats ok :)  
Click `Review and Launch`  
Click `Continue` to the port 22 warning, its fine!! 👍  
Click `Launch`  
Select `Proceed Without a key pair` and check the acknowledge box  
Click `Launch Instances`  
Click `View Instances`  

Wait for the instance to be in the `RUNNING` state  
_continue to phase 1C below while the instance is provisioning_

# Phase 1C - Create SSM Parameter Store values for wordpress

Storing configuration information within the SSM Parameter store scales much better than attempting to script them in some way.
In this sub-section you are going to create parameters to store the important configuration items for the platform you are building.  

Open a new tab to https://console.aws.amazon.com/systems-manager/home?region=us-east-1  
Click on `Parameter Store` on the menu on the left

## Create Parameter - DBUser (the login for the specific wordpress DB)  
Click `Create Parameter`
Set Name to `/Sunday/Wordpress/DBUser`
Set Description to `Wordpress Database User`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `Sundaywordpressuser`  
Click `Create parameter`  

## Create Parameter - DBName (the name of the wordpress database)  
Click `Create Parameter`
Set Name to `/Sunday/Wordpress/DBName`
Set Description to `Wordpress Database Name`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `Sundaywordpressdb`  
Click `Create parameter` 

## Create Parameter - DBEndpoint (the endpoint for the wordpress DB .. )  
Click `Create Parameter`
Set Name to `/Sunday/Wordpress/DBEndpoint`
Set Description to `Wordpress Endpoint Name`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `localhost`  
Click `Create parameter`  

## Create Parameter - DBPassword (the password for the DBUser)  
Click `Create Parameter`
Set Name to `/Sunday/Wordpress/DBPassword`
Set Description to `Wordpress DB Password`  
Set Tier to `Standard`  
Set Type to `SecureString`  
Set `KMS Key Source` to `My Current Account`  
Leave `KMS Key ID` as default.

Set `Value` to `1234567890.`  you can as well choose your own passwrd but!!!! MAKE SURE TO REMEMBER IT!!!!!!!!
Click `Create parameter`  

## Create Parameter - DBRootPassword (the password for the database root user, used for self-managed admin)  
Click `Create Parameter`
Set Name to `/Sunday/Wordpress/DBRootPassword`
Set Description to `Wordpress DBRoot Password`  
Set Tier to `Standard`  
Set Type to `SecureString`  
Set `KMS Key Source` to `My Current Account`  
Leave `KMS Key ID` as default
Set `Value` to `1234567890.`   you can as well choose your own passwrd but!!!! MAKE SURE TO REMEMBER IT!!!!!!!!
Click `Create parameter`  

# Phase 1D - Connect to the instance and install a database and wordpress
Right click on `Wordpress-Manual` instance choose `Connect`
Choose `Session Manager`  
Click `Connect`  
type `bash` and press enter  
type `cd` and press enter  
type `clear` and press enter

## Bring in the parameter values from SSM

Run the commands below to bring the parameter store values into ENV variables to make the manual build easier.  

```
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /Sunday/Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`
DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /Sunday/Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`
DBUser=$(aws ssm get-parameters --region us-east-1 --names /Sunday/Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`
DBName=$(aws ssm get-parameters --region us-east-1 --names /Sunday/Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`
DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /Sunday/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
```

## Install updates on your Machine

```
sudo yum -y update
sudo yum -y upgrade
```

## Install Pre-Reqs and Web Server 

```
sudo yum install -y mariadb-server httpd wget
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
sudo amazon-linux-extras install epel -y
sudo yum install stress -y
```

## Set DB and HTTP Server to running and to  start by default

```
sudo systemctl enable httpd
sudo systemctl enable mariadb
sudo systemctl start httpd
sudo systemctl start mariadb
```

## Set the MariaDB Root Password

```
sudo mysqladmin -u root password $DBRootPassword
```

## Download and extract Wordpress

```
sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
cd /var/www/html
sudo tar -zxvf latest.tar.gz
sudo cp -rvf wordpress/* .
sudo rm -R wordpress
sudo rm latest.tar.gz
```

## Configure the wordpress wp-config.php file 

```
sudo cp ./wp-config-sample.php ./wp-config.php
sudo sed -i "s/'database_name_here'/'$DBName'/g" wp-config.php
sudo sed -i "s/'username_here'/'$DBUser'/g" wp-config.php
sudo sed -i "s/'password_here'/'$DBPassword'/g" wp-config.php
```

## Fix Permissions on the filesystem

```
sudo usermod -a -G apache ec2-user   
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
sudo find /var/www -type d -exec chmod 2775 {} \;
sudo find /var/www -type f -exec chmod 0664 {} \;
```

## Create Wordpress User, set its password, create the database and configure permissions

```
sudo echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
sudo echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
sudo echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
sudo echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
sudo mysql -u root --password=$DBRootPassword < /tmp/db.setup
sudo rm /tmp/db.setup
```

## Test Wordpress is installed

Open the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Select the `Wordpress-Manual` instance  
copy the `IPv4 Public IP` into your clipboard  
Open that IP in a new tab  
You should see the wordpress welcome page  

## Perform Initial Configuration and make a post

in `Site Title` enter `Sunday-blog` or your preferred site title ¿  
in `Username` i recommend  using  `admin` but you can choose your preferred username thats fine!!!
in `Password` it should suggest a strong password for the wordpress admin user, feel free to use this or choose your own - regardless, write it down somewhere safe.  
in `Your Email` enter your email address  
Click `Install WordPress`
Click `Log In`  
In `Username or Email Address` enter `admin` or your previously used username 
in `Password` enter the previously noted down strong password  
Click `Log In`  

Click `Posts` in the menu on the left  
Select `Hello World!` 
Click `Bulk Actions` and select `Move to Trash`
Click `Apply`  

Click `Add New`  
If you see any popups close them down  
For title `Lets talk about Sunny-Sunday blog!`  or choose your preffered blog title
Click the `+` under the title, select  `Gallery` 
Click `Upload`  
Choose some nice pictures.... if you dont have any use google images to download some  
Upload them  
Click `Publish`  
Click `Publish`
Click `view Post`  

This is your working, manually installed and configured wordpress

# Phase 1 - FINISHED

This configuration has several limitations which you will resolve one by one within this lesson :-

- The application and database are built manually, taking time and not allowing automation
- ^^ it was slow and annoying ... that was the intention.
- The database and application are on the same instance, neither can scale without the other
- The database of the application is on an instance, scaling IN/OUT risks this media
- The application media and UI store is local to an instance, scaling IN/OUT risks this media
- Customer Connections are to an instance directly ... no health checks/auto healing
- The IP of the instance is hardcoded into the database ....
- Go to https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name
- Right click `Wordpress-Manual` , `Instance State`, `Stop`, `Yes, Stop`
- Right click `Wordpress-Manual` , `Instance State`, `Start`, `Yes, Start`
- the IP address has changed ... which is bad
- Try browsing to it ...
- What about the images....?
- The images are pointing at the old IP address...
-Now you can go and terminate your instance 

You can now move onto [Phase2](https://github.com/profebass99/aws-wordpress-evolution/blob/main/Design%20Steps%20&%20instructions/Phase-2.md)
