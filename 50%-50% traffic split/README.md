# 50%-50% traffic split

### Purpose of the folder
- This part of the repository creates webservers cluster(using EC2 and Auto Scaling) and load balancer in AWS. The load balancer listens on port 80 and returns "blue" or "green" text every time the web browser is refreshed.
------------------------------------------------------------------------------------------------
### List of files files in respository.
- main.tf - terraform configuration file that creates web servers cluster and load balancer.
- variables.tf - terraform configuration file with input variables.
- output.tf - terraform configuration file with output parameters.

---------------------------------------------------------------------------------------------------------------
###  Infrastructure resources to be created:
- webservers cluster - group of servers (in this case EC2 instances) that are connected with each other.
   - auto scaling group - automatically scale up or down group of resources (in this case EC2 instances).
- load balancer - distributes workload across the webservers cluster.
-----------------------------------------------------------------------------------------------------------------
### Terraform code resources explanation:
 #### webservers cluster and auto scaling group resources:
  - `aws_launch_configuration` - how to configure each EC2 instace(server) in auto scaling group(webservers cluster). 
  - `aws_autoscaling_group` - how many EC2 instances will be running, referencing the `aws_launch_configuration`.
 #### load balancer resources:
  - `aws_elb` - to distribute traffic across the `aws_autoscaling_group`.
  - `aws_security_group.elb` - security group that allows incoming traffic to `aws_elb` on ceratin port. 
  - `aws_security_group.instance` - security group that allows incominf traffic on each EC2 instance on certain port.

---------------------------------------------------------------------------------------------------------------
### How to use this repository:
- install `terraform` from [here](https://www.terraform.io/downloads.html).
- setup Amazon Web Services (AWS) account [here](https://aws.amazon.com/).
- configure your AWS access keys [here](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys).
- configure your AWS access keys as environment variables so you can authenticate to your AWS account:

```
export AWS_ACCESS_KEY_ID="your access key id here"
export AWS_SECRET_ACCESS_KEY="your secret access key here"
```
   
- clone the repository to your local computer: `git clone https://github.com/nikcbg/tf-blue-green-project`.
- go into the cloned repo on your computer: `cd tf-blue-green-project`.
- go into the subfolder of the repository: `cd 50%-50% traffic split`.
------------------------------------------------------------------------------------------------------------------
### How to change the code so you can redirect the traffic.
- you need modify `main.tf` file.
- you need to change `min_size` and `max_size` parameters of `aws_autoscaling_group` resources. 
- to split the traffic in half both `min_size` and `max_size` parameters of `aws_autoscaling_group` resources need to be set to 1. 
- this way the size of the autoscaling group is kept to 1 for both resources and traffic is split equally 

----------------------------------------------------------------------------------------------------------------------------
### Commands needed to build the webservers cluster and load balancer
- execute `terraform init` - to initialize the provider and download the neccesery plugins.
  
- execute `terraform plan` - to create execution plan for changes to be applied, the output should diplay the following:  
```
Terraform will perform the following actions:

+ aws_autoscaling_group.example_blue
+ aws_autoscaling_group.example_green
+ aws_elb.example
+ aws_launch_configuration.example_blue
+ aws_launch_configuration.example_green
+ aws_security_group.elb
+ aws_security_group.instance

Plan: 7 to add, 0 to change, 0 to destroy.
```
  
- execute `terraform apply` - to apply the desired changes, the output should diplay the following:

```
aws_security_group.instance: Creation complete after 9s (ID: sg-03f744133f3206ae4)
aws_security_group.elb: Creation complete after 9s (ID: sg-091ae73a79e916629)
aws_launch_configuration.example_green: Creation complete after 3s (ID: terraform-20190312072021877200000001)
aws_launch_configuration.example_blue: Creation complete after 4s (ID: terraform-20190312072023055100000002)
aws_elb.example: Creation complete after 17s (ID: terraform-asg-example)
aws_autoscaling_group.example_green: Creation complete after 49s (ID: tf-asg-20190312072038116200000003)
aws_autoscaling_group.example_blue: Creation complete after 52s (ID: tf-asg-20190312072038116800000004)

Apply complete! Resources: 7 added, 0 changed, 0 destroyed.

Outputs:

elb_dns_name = terraform-asg-example-123456789.us-east-1.elb.amazonaws.com

```
- execute `terraform destroy` - to destroy the resource that we just created.
