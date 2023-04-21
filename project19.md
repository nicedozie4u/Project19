# AUTOMATING INFRASTRUCTURE WITH IAC USING TERRAFORM PART 4 – TERRAFORM CLOUD
## INTRODUCTION
In this project, instead of running the Terraform codes in project 18 from a command line, rather it is being executed from Terraform cloud console. The AMI is built differently with packer while Ansible is used to configure the infrastructure after its been provisioned by Terraform.

The following outlines the steps:

## STEP 1: Setting Up A Terraform Account

- After verifying my email and creating an organisation in the terraform cloud site, then on the **configure workspace** page, selecting **version control workflow** option inorder to run Terraform commands triggered from my git repository.
- Creating a new repository called terraform-cloud and pushing my terraform codes developed in project 18 into the repository
- Connecting the workspace to the new repository created and clicking on **create workspace**



![](./images/create%20workspace01.png)
![](./images/create%20workspace02.png)
![](./images/create%20workspace03.png)
![](./images/create%20workspace04.png)


- Clicking on **configure variables** on the next page to setup my AWS credentials as environment variables.

![](./images/create%20workspace05.png)

- Now my Terraform cloud is all set to apply the codes from GitHub and create all necessary AWS resources.

## STEP 2: Building AMI With Packer

- Installing **packer** on my local machine:

![](./images/Install%20packer.png)

- Cloning the [repository](https://github.com/darey-devops/PBL-project-19.git) and changing directory to the AMI folder

- Running the packer commands to build AMI for Bastion server, Nginx server and webserver

**For Bastion Server**

![](./images/ami%20created.png)
![](./images/bastion%20ami%20created.png)

**For Nginx Server**

![](./images/creating%20nginx%20ami.png)

![](./images/nginx%20ami%20created.png)

**For Tooling and Wordpress Server**

![](./images/web%20ami%20creating.png)

![](./images/web%20ami%20created.png)

**For Jenkins, Artifactory and sonarqube Server**

![](./images/creating%20ubuntu%20ami.png)

![](./images/ububtu%20ami%20created.png)

![](./images/all%20amis%20created.png)

## STEP 3: Running The Terraform Cloud To Provision Resources

- Inputing the AMI ID in my **terraform.tfvars** file for the servers built with packer which terraform will use to provision Bastion, Nginx, Tooling and Wordpress server

![](./images/update%20terraform%20with%20new%20ami's.png)

- Pushing the codes to my repository will cause terraform cloud to trigger a plan
- Accepting the plan to to trigger an apply command

![](./images/git%20push.png)
![](./images/new%20push%20captured.png)
![](./images/run%20successful.png)
![](./images/confirm%20%26%20apply.png)
![](./images/confirm%20%26%20apply02.png)
![](./images/applying.png)
![](./images/applying02.png)

- Confirm infrastructure was created

![](./images/Instances%20created.png)
![](./images/Security%20groups%20creted.png)
![](./images/Load%20balancer%20created.png)
![](./images/S3%20bucket%20created.png)
![](./images/dynamodb%20table%20created.png)
![](./images/VPC%20created.png)
![](./images/Subnets%20created.png)
![](./images/routing%20tables.png)
![](./images/Cert%20created.png)


## STEP 4: Configuring The Infrastructure With Ansible

- After a successful execution of terraform apply, connecting to the bastion server through ssh-agent to run ansible against the infrastructure

![](./images/key.png)

![](./images/key02.png)

- Install Ansible

![](./images/install%20ansible.png)

- Updating the **nginx.conf.j2** file to input the internal load balancer dns name generated via terraform:

![](./images/update%20loadbalancer.png)

![](./images/update%20loadbalancer01.png)

- Updating the RDS endpoints, Database name, password and username in the **setup-db.yml** file for both the tooling and wordpress role

**EFS Details**

![](./images/copy%20rds%20endpoint.png)

![](./images/replace%20tooling%20endpoint.png)


- Updating the EFS Access point ID for both the wordpress and tooling role in the **main.yml**

**For Tooling**


![](./images/fstab%20for%20tooling.png)



![](./images/update%20fstab%20for%20tooling.png)

![](./images/update%20file%20system%20for%20tooling.png)

**For Wordpress**

![](./images/efs%20for%20wordpress.png)

![](./images/update%20fstab%20for%20wordpress.png)




- Verifying the inventory

![](./images/update%20roles%20path.png)

![](./images/edit%20config%20path.png)

![](./images/ansible-inventory%20graph.png)

- Exporting the environment variable **ANSIBLE_CONFIG** to point to the ansible.cfg from the repo and running the ansible-playbook command: `$ ansible-playbook -i inventory/aws_ec2.yaml playbook/site.yml`

![](./images/running%20ansible.png)
![](./images/running%20ansible02.png)
![](./images/running%20ansible03.png)

## STEP 5: Confirm connectivity from the bastion to other instances

![](./images/confirm%20ssh%20to%20private%20instance02.png)


![](./images/wordpress%20error%20resolved.png)

- Update listerners and autoscaling group

![](./images/uncomment%20alb.png)

![](./images/uncomment%20listener.png)

![](./images/uncomment%20listener03.png)

![](./images/uncomment%20autoscalling%20attach.png)

![](./images/uncomment%20autoscalling%20attach02.png)

![](./images/uncomment%20autoscalling%20attach04.png)

- git push config

![](./images/git%20push%20final.png)

- apply to create infrastructure

![](./images/confirm%20%26%20apply%20for%20listners.png)

- Confirm listeners are now added to Internal and external loadbalancers

![](./images/loadbalancer%20listner%20readded.png)

![](./images/loadbalancer%20listner%20added02.png)

- Confirm tooling, wordpress and nginx healthchecks are healthy.

![](./images/wordpress%20targetgroup%20healthcheck.png)

![](./images/tooling%20targetgroup%20helthcheck.png)

![](./images/restart%20nginx%20on%20nginx%20server.png)

![](./images/nginx%20targetgroup%20healthcheck%20healthy.png)

**STEP 6: Testing the Infrastructure**

![](./images/wordpress%20successful.png)

![](./images/tooling%20successful.png)

![](./images/gitlab%20pipelines%20running.png)







- Destroying the Infrastructure

![](./images/destroy%20infrastructure.png)

