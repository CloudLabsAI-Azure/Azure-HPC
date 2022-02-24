![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Azure HPC OnDemand Platform
</div>

<div class="MCWHeader2">
Hands-on lab guide
</div>

<div class="MCWHeader3">
January 2022
</div>

<!-- TOC -->

- [Azure HPC OnDemand Platform lab guide](#azure-hpc-on-demand-platform-lab-guide)
   - [Requirements](#requirements)
      - [Getting Started](https://github.com/CloudLabsAI-Azure/Azure-HPC/blob/main/README.md#getting-started)
      - [Exercise 1: Prepare for implementing Azure HPC OnDemand Platform environment](#exercise-1-prepare-for-implementing-azure-hpc-ondemand-platform-environment)
      - [Task 1: Deploy Azure Bastion](#task-2-deploy-azure-bastion)
   - [Exercise 2: Implement Azure HPC OnDemand Platform infrastructure](#exercise-2-implement-azure-hpc-ondemand-platform-infrastructure)
      - [Task 1: Customize infrastructure components](#task-1-customize-infrastructure-components)
      - [Task 2: Deploy Azure HPC OnDemand Platform cloud infrastructure](#task-2-deploy-azure-hpc-ondemand-platform-cloud-infrastructure)
      - [Task 3: Build images](#task-3-build-images)
      - [Task 4: Review deployment results](#task-4-review-deployment-results)
      - [Task 5: Generate passwords for user and admin accounts](#task-5-generate-passwords-for-user-and-admin-accounts)
   - [Exercise 3: Install and configure Azure HPC OnDemand Platform software components](#exercise-3-install-and-configure-azure-hpc-ondemand-platform-software-components)
      - [Task 1: Install Azure HPC OnDemand Platform software components](#task-1-install-azure-hpc-ondemand-platform-software-components)
      - [Task 2: Review installation results](#task-2-review-installation-results)
   - [Exercise 4: Validate functionality of Azure HPC OnDemand Platform](#exercise-4-validate-functionality-of-azure-hpc-ondemand-platform)
      - [Task 1: Create jobs based on the default Azure HPC OnDemand Platform template](#task-1-create-jobs-based-on-the-default-azure-hpc-ondemand-platform-template)
      - [Task 2: Create jobs based on a non-default Azure HPC OnDemand Platform template](#task-2-create-jobs-based-on-a-non-default-azure-hpc-ondemand-platform-template)
   - [Exercise 5: Deprovision Azure HPC OnDemand Platform environment](#exercise-5-deprovision-azure-hpc-ondemand-platform-environment)
      - [Task 1: Terminate the cluster](#task-1-terminate-the-cluster)
     

<!-- /TOC -->

# Azure HPC OnDemand Platform lab guide

## Requirements

- A Microsoft Azure subscription
- A work or school account with the Owner role in the Azure subscription
- A lab computer with:

   - Access to Microsoft Azure
   - A modern web browser (Microsoft Edge, Google Chrome, or Mozilla Firefox)


### Getting Started

1. In the virtual machine (VM) on the left, click on the Azure Portal as shown below.

   ![Azure Portal](https://raw.githubusercontent.com/CloudLabsAI-Azure/AIW-Azure-Network-Solutions/main/media/gs4.png)
   
2. When you click on Azure portal, the edge browser welcome screen will come up, select Start without your data.

   ![Start without data](../main/media/start%20without%20data.png)
   
3. On the next window, click on Confirm and start browsing.

   ![](../main/media/confirmandstart.png)
   
4. Now, you will see two tabs in the edge browser, close the first tab named with Microsoft Edge.

5. On the Sign in to Microsoft Azure tab you will see a login screen, in that enter the following email/username and then click on Next.

    * Email/Username: <inject key="AzureAdUserEmail"></inject>
   
     ![](../main/media/odl.png)
     
1. Now enter the following password and click on **Sign in**.

   * Password: <inject key="AzureAdUserPassword"></inject>
   
     ![](../main/media/password.png)


## Exercise 1: Prepare for implementing Azure HPC OnDemand Platform environment

Duration: 10 minutes

In this exercise, you will set up an Azure VM that will be used for the deployment of the lab environment. 


### Task 1: Deploy Azure Bastion 

> **Note**: Azure Bastion allows for connection to Azure VMs without relying on public endpoints, protecting against brute force exploits that target operating system level credentials.



1. Type **Bastions (1)** in the search box of the Azure portal menu, and select **Bastions (2)**.

     ![](../main/media/bastions.png)
     
4. On the **Bastions** blade, select **+ Create Bastion**.

     ![](../main/media/create%20bastion.png)
     
6. On the **Basic** tab of the **Create a Bastion** blade, specify the following settings and select **Review + create**:

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|**Azure-hpc**|
   |Name|**azure-hpc-bastion**|
   |Region|**Use the same region as your Resource group**|
   |Tier|**Basic**|
   |Virtual network|**azcli-vnet**|
   |Subnet|**AzureBastionSubnet (192.168.3.0/24)**|
   |Public IP address|**Create new**|
   |Public IP name|**azhop-cli-pip**|
   |Review + Create| Click on **Review + create**|
   
    ![](../main/media/re%2Bcr.png)
   
7. Review the configuration and click on **Create**.

   ![](../main/media/create.png)

   > **Note**: Wait for the deployment to complete before you proceed to the next exercise. The deployment might take about 5 minutes.


### Task 2: Connect to the VM

> **Note**: Ensure that your browser has the pop-up functionality enabled before you attempt to connect via Azure Bastion.

1. Type **Virtual machines (1)** in the search box of **Azure Portal** and select **Virtual machines (2)**.

     ![](../main/media/virtual%20machines.png)
     
1. On the **Virtual machines** page, select **azcli-vm0**.

    ![](../main/media/selectvm.png)
    
1. On the **azcli-vm0** blade, click on **Connect (1)** and select **Bastion (2)** from the drop-down menu.

   ![](../main/media/connect.png) 
   
1. On **Bastion** page, enter the below details:

    - **User name** : **azureadm (1)**
    - **Password** : **Password.1!! (2)**
    - Click on **Connect (3)**

     ![](../main/media/connectbastion.png)

>**Note:**  In case you are getting error while connecting to vm using bastion, please click on blocked pop-up and select **Always Allow pop-ups and redirects from https://portal.azure.com** .

![](https://raw.githubusercontent.com/CloudLabsAI-Azure/Azure-HPC/main/media/bastion-02.png)

## Exercise 2: Implement Azure HPC OnDemand Platform cloud infrastructure

Duration: 90 minutes

In this exercise, you will deploy the Azure HPC OnDemand Platform infrastructure.

### Task 1: Customize infrastructure components

An az-hop environment is defined by using a configuration file named **config.yml**, which needs to reside in the root of the repository. The simplest way to create is by cloning the template file **config.tpl.yml** and modifying the content of the copied file. In this task, you will review its content and, if needed, set the target Azure region for deploying the Azure HPC OnDemand Platform infrastructure.

1. On the lab VM, within the SSH session, run the following commands to copy **config.tpl.yml** to **config.yml**

```bash
 cd
 git clone --recursive https://github.com/Azure/az-hop.git
 cd az-hop
 cp config.tpl.yml config.yml
 ```
      

2. Open the **config.yml** file using your preferred editor (such as Nano or vi)

   ```bash
   nano vi config.yml
   ```

3. Review the content of the **config.yml** file and note that it includes the following settings:
 >**Note:**  Please run the below command to retrieve the location: .

    az account list-locations -o table

   - **location**: Update the location with the name of your resource group location.
   - **resource_group**: Update the resource group name as **Azure-hpc**
   - **use_existing_rg** : Update the value to **true** to use the existing resource group.
   - **anf**: configuration properties of Azure NetApp Files resources
   - **admin_user**: the name of the admin user account (its random password is auto-generated and stored in the Azure Key vault provisioned as part of the deployment
   - **network**: configuration properties of the Azure virtual network into which the Azure resources hosting the infrastructure resources are deployed
   - **jumpbox**, **ad**, **ondemand**, **grafana**, **scheduler**, **cyclecloud**, **winviz**, and **lustre**: configuration properties of Azure VMs hosting the infrastructure components
   - **users**: user accounts auto-provisioned as part of the deployment
   - **queue_manager**: the name of the scheduler to be installed and configured (**openpbs** by default)
   - **authentication**: the authentication method (**basic** by default, but you have the option of using OpenID Connect instead)
   - **images**: the list of images that will be available for deployment of computing cluster nodes and their respective configuration
   - **queues**: the list of node arrays of CycleCloud and their respective configuration

4. After updating the **config.yml** file, save the change, and close the file.

### Task 2: Deploy Azure HPC OnDemand Platform infrastructure


1.  **Please run 'az login' to setup account.**, follow the below steps:

     - Run the below command to login into **Azure Portal**

     ```bash
     az login
     ```
     - Note the code displayed in the output of the command, open another tab in the browser within your lab VM, navigate to [the Microsoft Device Login page](https://microsoft.com/devicelogin), enter the code, and select **Next**.

        ![](../main/media/code.png)
     - Now click on **<inject key="AzureAdUserEmail" enableCopy="false"></inject>**

        ![](../main/media/pickaccount.png)
       
     - On **Are you trying to sign in to Microsoft Azure CLI** page, click on **Continue** to sign in into Azure Portal.

        ![](../main/media/continue.png)
        
        
2.1. Within the SSH session, run the following command to generate a Terraform deployment plan that includes the listing of all resources to be provisioned:

   ```bash
   ./build.sh -a plan
   ```
  
    >**Note:** In case you get an error while running the above command, run the command mentioned below and re-perform step-1.

     ```bash
     sudo su
     ```
3. Review the generated list of resources and then run the following command to trigger the deployment of the Azure HPC OnDemand Platform infrastructure:

   ```bash
   ./build.sh -a apply
   ```

     > **Note**: Wait for the deployment to complete. This should take about 20 minutes. 

### Task 3: Build images

The az-hop solution provides pre-configured Packer configuration files that can be used to build custom images. The utility script **./packer/build_image.sh** performs the build process and stores the resulting images in the Shared Image Gallery included in the deployed infrastructure.

1. Within the SSH session, run the following commands to build a custom image based on the **azhop-centos79-v2-rdma-gpgpu.json** configuration file.

   > **Note**: The image creation process based on the **azhop-centos79-v2-rdma-gpgpu.json** configuration file relies on a **Standard_d8s_v3** SKU Azure VM.

   ```bash
   cd packer/
   ./build_image.sh -i azhop-centos79-v2-rdma-gpgpu.json
   ```

   > **Note**: Disregard any warning messages.

   > **Note**: Wait for the process to complete. This might take about 20 minutes.

   > **Note**: The image creation process based on the **centos-7.8-desktop-3d.json** configuration file relies on a **Standard_NV6** SKU Azure VM. If you managed to obtain a sufficient number of quotas to provision a **Standard_NV6**-based Azure VM, you can proceed directly to the next step. Otherwise, open the file **centos-7.8-desktop-3d.json**, replace the entry **Standard_NV6** with **Standard_D8s_v3**, save the change, and close the file. While such a step is likely to affect the functionality of computing resources based on this image, it is meant strictly as a workaround that facilitates the next step in the process of implementing the Azure HPC OnDemand Platform lab environment.

1. Now run the following command to build a custom image based on the **centos-7.8-desktop-3d.json** configuration file.

   ```bash
   ./build_image.sh -i centos-7.8-desktop-3d.json
   ```

   > **Note**: Wait for the process to complete. This might take about 40 minutes.


### Task 4: Review deployment results

1. On the lab VM, navigate back to **Azure Portal** and search for **Azure compute galleries** then select it.

    ![Azue Compute gallery](../main/media/AcG.png)

1. On the **Azure compute galleries** blade, select the entry whose name starts with the prefix **azhop**.
 
    ![azhop](../main/media/azhop.png)
    
3. Now on the **Azure Compute gallery** blade, scroll down and verify that the gallery includes two VM image definitions. 

     ![image defination](../main/media/image%20defination.png)
     
4. Navigate back to the Home page and type **Virtual machines (1)** in the search box of **Azure Portal** then select **Virtual machines (2)**.

     ![](../main/media/virtual%20machines.png)   
     
6. On the **Azure virtual machines** blade, review the listing of the provisioned virtual machines.

    ![list of VMs](../main/media/listofvm.png)

1. Close the newly opened browser tab displaying the **Azure virtual machines** blade in the Azure portal.

### Task 5: Generate passwords for user and admin accounts

1. On the lab VM, within the SSH session, run the following command to generate the password for the **clusteradmin** and **clusteruser** accounts defined in the **config.yml** configuration file:

   ```bash
   cd ..
   ./create_passwords.sh
   ```

1. Run the following command to display the newly generated password of the **clusteradmin** account:

   ```bash
   ./bin/get_secret clusteradmin
   ```

   > **Note**: Note the **Password** in notepad. You will be using it in later tasks.

   > **Note**: The **./bin/get_secret utility script** retrieves the password of the user you specify as its parameter from the Azure Key vault which is part of the Azure HPC OnDemand Platform infrastructure you deployed in the previous exercise.

## Exercise 3: Install and configure Azure HPC OnDemand Platform software components

Duration: 60 minutes

In this exercise, you will install and configure software components that form the Azure HPC OnDemand Platform solution.

> **Note**: The installation is performed by using Ansible playbooks and supports the setup of individual components as well as the setup of the entire solution. In either case, it is necessary to account for dependencies between components. The setup script **install.sh** in the root directory of the repository installs in the intended order. The components include: **ad**, **linux**, **add_users**, **lustre**, **ccportal**, **cccluster** (this component requires that custom images are present in the compute gallery), **scheduler**, **ood**, **grafana**, **telegraf**, and **chrony**.

### Task 1: Install and configure Azure HPC OnDemand Platform software components

1. Run the following command in the SSH session to invoke the installation of the Azure HPC OnDemand Platform software components:

   ```bash
   ./install.sh
   ```

   > **Note**: Wait for the process to complete. This might take about 30 minutes.

   > **Note**: In case of a transient failure, the install script can be rerun since most of the settings are idempotent. In addition, the script has a checkpointing mechanism that creates component-specific files with the **.ok** extension in the playbooks directory and checks for their presence during subsequent runs. If you want to re-apply the configuration to an existing component, delete the corresponding **.ok** file and rerun the install script.

### Task 2: Review installation results

1. Once the installation completes, within the SSH session , run the following command to display the URL of the Azure HPC On-Demand Platform portal:

   ```bash
   grep ondemand_fqdn playbooks/group_vars/all.yml
   ```

   > **Note**: Note the **ondemand_fqdn** in the notepad. You will need it in the further steps.

1. Within the lab VM, open a new tab in the browser, navigate to the URL of the Azure HPC On-Demand Platform portal you identified earlier in this task.
 
     ![](../main/media/ondemandsite.png)
     
3. When you are prompted to sign in use the below credentials:

    - **Username**: **clusteradmin** 
    - **Password** : Enter the **Password** you identified in the previous step.

   > **Note**: You will be presented with the **Azure HPC On-Demand Platform** dashboard. Review its interface, starting with the top level menu, which provides access to **Apps**, **Files**, **Jobs**, **Clusters**, **Interactive Apps**, **Monitoring**, and **My Interactive Sessions** menu items.

     ![](../main/media/ondemand%20signin.png)

1. On the **Azure HPC On-Demand Platform portal** Dashboard page, click on the **Monitoring** menu and select **Azure CycleCloud** from the drop-down list.

   ![](../main/media/azure%20cycle%20cloud.png)

    
4. Now you will navigate to the page titled **App has not been initialized or does not exist** and select **Initialize App**.

   > **Note**: This prompt is a reflection of the architecture of the OnDemand component, which the Azure HPC OnDemand Platform solution relies on to implement its portal. The shared front-end creates Per User NGINX (PUN) processes to provide connectivity to such components as **Azure CycleCloud**, **Grafana**, or **Robinhood Dashboard**. 

     ![](../main/media/initialize%20app.png)

1. On the **Azure CycleCloud for Azure HPC On-Demand Platform** page, note that presents a configuration of a cluster named **pbs1**. 

    ![](../main/media/pbs1.png)
    
3. On the **pbs1** page, select the **Arrays** tab and note that it contains six entries representing queue definitions you reviewed earlier in the **config.yml** file. 

   ![](../main/media/arrays.png)


## Exercise 4: Validate functionality of Azure HPC OnDemand Platform

Duration: 30 minutes

In this exercise, you will validate the functionality of the Azure HPC OnDemand Platform.

### Task 1: Create jobs based on the default Azure HPC OnDemand Platform template

1. On the lab VM, navigate back to **Azure HPC On-Demand Platform portal** Dashboard page, and select **Jobs** from the menu title the select **Job Composer** from the drop-down list.

    ![](../main/media/job%20composer.png)
    
3. On the **Jobs** page, select **+ New job** the select **From Default Template** from the drop-down list.

   ![](../main/media/nrejob.png)

   > **Note**: This will automatically create a job named **(default) Sample Sequential Job** that targets the **execute** CycleCloud array. To identify the content of the job script, ensure that the newly created job is selected and review the **Script contents** pane.

1. Repeat the previous step twice to create two additional jobs based on the default template.

   > **Note**: The default job template contains a trivial script that runs `echo "Hello World"`.

1. Note that all three jobs are currently in the **Not Submitted** state. To submit them, select each one of them and select **Submit**. 

   > **Note**: The status of the jobs should change to **Queued**.

   ![](../main/media/submit.png)
   
1. On the lab VM, navigate back to **Azure HPC On-Demand Platform portal** Dashboard page, click on the **Monitoring** menu and select **Azure CycleCloud** from the drop-down list.

   ![](../main/media/azure%20cycle%20cloud.png)

1. Monitor the status of the cluster and note that the number of nodes increases to **3**, which initially are listed in the **acquiring** state.

   ![](../main/media/nodes%203.png)
   
3. On the **Nodes** tab, verify that **execute** appears in the **Template** column, the **Nodes** column contains the entry **3**, and the **Last status** column displays the **Creating VM** message.

   ![](../main/media/creatingvm.png)
   
5. On the **pbs1** tab of the **Azure CycleCloud for Azure HPC On-Demand Platform** portal, select **Scalesets** tab and note a scaleset that hosts the cluster nodes with its size set to **3**.

    ![](../main/media/scaleset.png)
    
7. Select the entry on the **Nodes** tab and review the details of the cluster nodes in the lower section of the page, including the name of each node, their status, the number of cores, and the placement group.


9. Navigate back to the **Azure HPC On-Demand Platform** dashboard page and select the **Jobs** from the menu then select **Active jobs** from the drop-down list.

    ![](../main/media/activejobs.png)
    
11. On the **Active jobs** page, verify that there are three active jobs listed in the **Queued** status.
12. Navigate back to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the progress of node provisioning.

     > **Note**: Wait until the status of nodes changes to **Ready**. This should take about 5 minutes.

1. Once the nodes are listed with the **Ready** status, switch back to the **Active jobs** page, refresh it, and note that the jobs are no longer listed there.

    > **Note**: If the jobs are still listed as queued, wait for a few minutes and refresh the page again. 

1. Navigate back to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the node status until it changes to terminating, resulting eventually in their deletion. 
1. On the **pbs1** page of the **Azure CycleCloud for Azure HPC On-Demand Platform** portal, select **Scalesets** tab and note that the scaleset hosting the cluster nodes persists but its size is set to **0**.

    ![](../main/media/size%20is%200.png)

### Task 2: Create jobs based on a non-default Azure HPC OnDemand Platform template

1. Navigate back to the **Azure HPC On-Demand Platform** dashboard page, select the **Jobs** from the menu and select **Job Composer** from the drop-down list.

   ![](../main/media/job%20composer.png)
   
3. On the **Job Composer** page, select **Templates**.

    ![](../main/media/templates.png)
    
3. On the **Templates** page, under the list of predefined templates, select the **Intel MPI PingPong** entry and then select **Copy Template**.

     ![](../main/media/copy%20template.png)

     > **Note**: The Message Passing Interface (MPI) ping-pong tests measure network latency and throughput between nodes on the cluster by sending packets of data back and forth between paired nodes repeatedly. The latency is the average of half of the time that it takes for a packet to make a round trip between a pair of nodes, in microseconds. The throughput is the average rate of data transfer between a pair of nodes, in MB/second. 

1. On the **New Template** page, specify the following settings and select **Save**:

   |Setting|Value|
   |---|---|
   |Name|**Intel MPI PingPong v2**|
   |Cluster|**AZHOP - Cluster**|
   |Note|**<p>A very basic template for running Intel MPI ping pong on hb120v2</p>**|
   
   ![](../main/media/new%20template.png)

1. Navigate back to the **Templates** page, select the newly created template and then select **View Files**.

    ![](../main/media/view%20files.png)
    
3. On the page listing the files that are part of the template, in the **pingpong.sh** row, select the square icon containing the vertical ellipsis symbol **(1)** and then, in the drop-down menu, select **Edit (2)**.

    ![](../main/media/edit.png)
    
5. On the page displaying the content of the **pingpong.sh** script, in the third line, replace `slot_type=hb120v3` with `slot_type=hb120v2` **(1)** and select **Save (2)**.

    ![](../main/media/slot%20type.png)
    
7. Navigate back to the **Jobs (1)** page, select **+ New job (2)**, in the drop-down menu, select **From Template (3)**, 
  
    ![](../main/media/from%20template.png) 
    
10. On the **Templates** page, ensure that the **Intel MPI PingPong v2** entry is selected **(1)**, and then select **Create New Job (2)**.

     > **Note**: This will automatically create a job named **Intel MPI PingPong v2** that targets the **hb120v2** CycleCloud array. 

     ![](../main/media/create%20new%20job.png)

1. Repeat the previous step twice to create two additional jobs based on the **Intel MPI PingPong v2** template.


3. Note that, as before, all three jobs are currently in the **Not Submitted** state. To submit them, select each one of them and select **Submit**. 

   > **Note**: The status of the jobs should change to **Queued**.

   ![](../main/media/submit1.png)

1. Navigate to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the progress of node provisioning.

   > **Note**: Wait until the status of nodes changes to **Ready**. This should take about 5 minutes.

   ![](../main/media/nodes%20provision.png)

1. Once the nodes are listed with the **Ready** status, switch back to the **Active jobs** page, refresh it, and note that the jobs are no longer listed there.

   > **Note**: If the jobs are still listed as queued, wait for a few minutes and refresh the page again. 

    ![](../main/media/nodata.png)

1. Navigate back to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the node status until it changes to terminating, resulting eventually in their deletion. 
  
     ![](../main/media/deletingvm.png)
     
3. On the **pbs1** page of the **Azure CycleCloud for Azure HPC On-Demand Platform** portal, select **Scalesets** tab and note that the scaleset hosting the cluster nodes persists but its size is set to **0**.
4. To review the output of the job, switch back to the **Azure HPC On-Demand Platform** dashboard, select the **Jobs** menu, and select **Job Composer** from the drop-down list.

    ![](../main/media/job%20composer.png)
    
6. On the **Jobs** page, select any of the **Intel MPI PingPong v2** job entries and, in the **Folder Contents** section, select **PingPong.o3**. 

   > **Note**: This will automatically open another web browser tab displaying the output of the job.

   ![](../main/media/pingpong.png)

## Exercise 5: Deprovision Azure HPC OnDemand Platform environment

Duration: 30 minutes

In this exercise, you will deprovision the Azure HPC OnDemand Platform lab environment.

### Task 1: Terminate the cluster

1. In the lab VM, navigate back to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal.


3. On the **pbs1** page, select **Terminate** and, when prompted for confirmation, select **OK**.

   ![](../main/media/terminate.png)
  
   
5. Monitor the progress of terminating the cluster.

   > **Note**: Ensure that all nodes and scaleset are deleted before you proceed to the next step.


