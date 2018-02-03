# Assumptions #

* Both application and database are deployed in AWS
* Workload is spread across application servers using an ELB.
* Assuming a spring boot application runtime required is Java.
* Although AWS provides cloudwatch for centralized logging, I am assuming Sumologic as it is a very powerful tool for working with and analysing logs
* Datadog is used as the centralized monitoring system, which can be used with AWS cloudwatch metrics as well as can be used to write custom monitors. 
* For database I will use AWS Aurora
* For setting up infrastructure, I will use Terraform

# Design #
## Provisioning ##
Provisioning the servers can be a two-step process - deploying new code in a new ASG and remove old from the ELB. However i think we should either go the route of doing blue/green deploy or using a canary deployment. 

I am assuming a canary deploy where we deploy the new ASG with only a small number of instances, wait for the instances to be marked healthy in the ELB (simple HTTP check) and then run some tests or check logs for any unexpected behaviour.
Once the canaries look good, scale the new ASG and then scale down the old one. 

To reduce human effort as well as avoid any human errors we will need to write a provisining tool. In the beginning personally I will try to after doing each step manually will make a note all the checks that I perform throughout the provisioining cycle and start coding them one by one as simple function calls.
Will then iterate over the provisiong process by automating small chunks and updating/adding checks as required.

Over the time the tool can be refactored and modularized to allow deployment of any similar application. 

Will also try to account for unknown variables add due to AWS such as limited instances, API timeouts etc.

## SSH Access ##
A simple way to provides SSH access would be to create a key-pair and share it with developers. Couple of drawbacks I see with this approach are access control and key rotation. This approach will also need that the servers are deployed in a public subnet.
Instead I will first setup a jumpbox in public subnet that has access to instances deployed in private subnet.

One way I can implement is maintaining a yaml file which contains user name, groups and their public key and is version controlled. A script can be written to pull down the repo and re-write authorized keys every 10 minutes if there is an change made to the repo. This script can be baked into the base AMI and cron job added to run it every 10 minutes. Script can be called by user config during first startup.
This way any user whose account has been removed from the yaml file will have his keys removed from all the boxes after 10 minutes. 

## Centralized Logging and monitoring ##

I would create a base AMI image that has up-to-date packages as well as sumologic, datadog pre installed. The AMI will also contain scripts that can consume user-specified yaml files for setting up custom logging and monitoring. 
One way the scripts could do it is by based on instance tag that contains the application name. Script will then check in a particular bucket(separate buckets for logging and monitoring) for folder named after the service and pull down the yaml file. Once again these scripts can be run as cron job which will check every 30 minutes for any changes to the bucket and will pull down new files when required. I would also enable versioning on the bucket.


## Support extra load ##
To support extra load autoscaling group settings can be pre set to scale the number of instances based on various criteria. for e.g CPU load. On the database side AWS aurora provides a range of instances to select from and upto 15 replicas per region.


## Infrastructure ##

Infrastrucutre required to support the application will be setup using terraform hopefully which over time can evolve into modules that can help setup similar services using much easily.

Setting up the resources for the first time using terraform will be maybe 2-3  days effort. Provisioning the serves manually or cloudformation for the first time would be only a few hours whereas the script to automate the deployment with canary deployment will be in it's simplest form (with required fail safes) will be 3-4 days. We can also bake in basic logging and datadog monitoring by adding basic checks as part of the base AMI. Developers' help will be required to determine and configure logging and monitoring for things they actually care about based on the application.



