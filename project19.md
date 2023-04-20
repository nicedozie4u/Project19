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

![](./images/efs%20for%20wordpress.png)

![](./images/update%20fstab%20for%20wordpress.png)

![](./images/update%20file%20system%20for%20wordpress.png)

![](./images/fstab%20for%20tooling.png)

![](./images/update%20fstab%20for%20tooling.png)

![](./images/update%20file%20system%20for%20tooling.png)

![](./images/ansible%20error.png)

**For Tooling**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/updating%20setup-db%20for%20tooling.png)

**For Wordpress**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/updating%20setup-db%20for%20wordpress.png)

- Updating the EFS Access point ID for both the wordpress and tooling role in the **main.yml**

**For Tooling**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/updating%20the%20efs%20point%20of%20tooling.png)

**For Wordpress**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/updating%20the%20efs%20point%20of%20wordpress.png)

- Verifying the inventory

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/ansible%20inventory%20graph.png)

- Exporting the environment variable **ANSIBLE_CONFIG** to point to the ansible.cfg from the repo and running the ansible-playbook command: `$ ansible-playbook -i inventory/aws_ec2.yml playbook/site.yml`

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/running%20ansible%20playbook.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/running%20ansible%20playbook-2.png)

## STEP 5: Setting Slack Notification For trigger Events

- From the notification option in the settings tab, selecting slack option

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/setting%20up%20slack%20notification-1.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/setting%20up%20slack%20notification-2.png)

- Creating the webhook to use to setup the notification

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/setting%20up%20slack%20notification-3.png)

## STEP 6: Setting The Terraform Cloud To Execute Only From 'Dev' Branch

- Creating 3 branches dev, prod and test 

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/3%20branches%20created.png)

- On the version control page in the settings tab, setting the **VCS branch** to **dev**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/running%20trigger%20from%20dev%20branch.png)

- After pushing the code to the dev branch the terraform plan command is triggered and notifications received from slack:

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/notifications%20received.png)

- Destroying the infrastructure by clicking on **Destruction and Delete** option in the settings tab:

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/destroying%20the%20infrastructure.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/destroying%20the%20infrastructure-2.png)

## STEP 7: Working With Terraform Private Module Registry

- Forking the repo from [hashicorp](https://github.com/hashicorp/learn-private-module-aws-s3-webapp)
- Then under my repository's tab, clicking on **tag** to create tag, clicking 'Create a new release' and adding 1.0.0 to the tag version field setting the release title to "First module release"

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/creating%20release%20tag.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/creating%20release%20tags-2.png)

- Clicking **Publish release** to create the release
- To create a Terraform module for my private module registry in the [terraform registry site]
(), navigating to the Registry header in Terraform Cloud and selecting **Publish private module** from the upper right corner.
- Selecting the GitHub(Custom) VCS provider that I configured and choosing the name of the module repository **terraform-aws-s3-webapp** and clicking the **Publish module** button.

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/signing%20in%20to%20terraform%20registry.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/publishing%20a%20module.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/creating%20second%20workspace.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/creating%20second%20workspace-2.png)

- To create a configuration that uses the module; forking the repo [learn private module](https://github.com/hashicorp/learn-private-module-root/) which will access the module published and Terraform will use it to create the infrastructure.

**main.tf**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/main.tf.png)

**variables.tf**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/variables.tf.png)

**outputs.tf**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/outputs.tf.png)

- Creating a new workspace and selecting the **learn-private-module-root** repository

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/20-creating%20workspace.png)

- Clicking on **Configure Variable** to set my AWS credentials as environment variable and also set the values of these variables;**region, prefix and name**, which is specified in the root module configuration

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/creating%20variables.png)

- Deploying the infrastructure by clicking on **start new plan**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/22-running%20the%20terraform%20apply.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/22-running%20the%20terraform%20apply-2.png)

**Testing the Infrastructure**

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/testing%20the%20deployment.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/testing%20the%20deployment-2.png)

- Destroying the Infrastructure

![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/23-terraform%20destroy.png)
![](https://github.com/somex6/Darey.io-Projects/blob/main/img/project19/23-terrafrorm%20destroy-2.png)