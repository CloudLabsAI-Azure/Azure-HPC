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
   |Resource group|**Azure-hpc-<inject key="DeploymentID" enableCopy="false" />**|
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

## Exercise 5: Set up Spack 

Duration: 30 minutes

In this exercise, you will install and configure Spack from Code Server, as documented in (https://azure.github.io/az-hop/tutorials/spack.html)[https://azure.github.io/az-hop/tutorials/spack.html]

### Task 1: Create a compute node

1. On the lab computer, in the browser window, switch to the tab displaying the **Azure HPC On-Demand Platform** portal, select the **Interactive Apps** menu, and in the drop-down menu, select **Code Server**.

   > ![Note]: This will open another browser tab displaying the Code Server launching page.

1. On the **Code Server** launching page, in the **Maximum duration of your remote session** list, enter **3**, in the **Slot Type** text box, type **execute**, and then select **Launch**.

   > ![Note]: This will initiate provisioning of a compute node of the type you specified. Note that this creates a new job, which **Queued** status is displayed on the same page.

1. Switch to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the progress of the **execute** node provisioning.

   > ![Note]: Wait until the status of the node changes to **Ready**. This should take about five minutes.

1. Switch back to the **Code Server** launching page, verify that the status of the corresponding job has changed to **Running** and select **Connect to VS Code**.

   > ![Note]: This will open another browser tab displaying the Code Server interface.

1. Review the interface, close the **Welcome** tab, select the **Application menu** in the top left corner of the page, in the drop-down menu, select **Terminal** and then, in the cascading menu, select **New Terminal**.
1. In the Terminal pane, at the **[clusteradmin@execute-1 ~]$** prompt, run the following command to clone the azurehpc repo and use the azhop/spack branch:

   ```bash
   git clone https://github.com/Azure/azurehpc.git
   ```

1. In the Terminal pane, run the following command to provision a compute node and connect to it interactively:

   ```bash
   qsub -l select=1:slot_type=hb120v3 -I
   ```

   > ![Note]: Wait for the node to be provisioned. This might take about 10 minutes.

### Task 2: Install Spack

1. On the lab computer, in the browser window displaying the Code Server, wait until the prompt **[clusteradmin@hb120v3-1 ~]$** appears within the Terminal pane.
1. In the Terminal pane, run the following commands to install and configure Spack:

   ```bash
   ~/azurehpc/experimental/azhop/spack/install.sh
   ~/azurehpc/experimental/azhop/spack/configure.sh
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hhb120v3-1 ~]$ ~/azurehpc/experimental/azhop/spack/install.sh
   Cloning into '/anfhome/clusteradmin/spack'...
   remote: Enumerating objects: 356062, done.
   remote: Counting objects: 100% (8/8), done.
   remote: Compressing objects: 100% (7/7), done.
   remote: Total 356062 (delta 0), reused 5 (delta 0), pack-reused 356054
   Receiving objects: 100% (356062/356062), 161.30 MiB | 20.68 MiB/s, done.
   Resolving deltas: 100% (151608/151608), done.
   Checking out files: 100% (9104/9104), done.
   Checking out files: 100% (8002/8002), done.
   Branch releases/v0.16 set up to track remote branch releases/v0.16 from origin.
   Switched to a new branch 'releases/v0.16'
   [clusteradmin@hb120v3-1 ~]$ ~/azurehpc/experimental/azhop/spack/configure.sh
   Add GCC compiler
   ==> Added 1 new compiler to /anfhome/clusteradmin/.spack/linux/compilers.yaml
       gcc@9.2.0
   ==> Compilers are defined in the following files:
       /anfhome/clusteradmin/.spack/linux/compilers.yaml
   Configure external MPI packages
   Configure local settings
   ```

1. In the Terminal pane, run the following commands to confirm the list of defined compilers:

   ```bash
   . ~/spack/share/spack/setup-env.sh
   spack compilers
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hb120v3-1 ~]$ . ~/spack/share/spack/setup-env.sh
   [clusteradmin@hb120v3-1 ~]$ spack compilers
   ==> Available compilers
   -- gcc centos7-x86_64 -------------------------------------------
   gcc@9.2.0
   ```

   > ![Note]: Verify that gcc 9.2 is listed.

## Exercise 6: Build, run, and analyze reservoir simulation using OPM Flow

In this exercise, you will build, run, and analyze reservoir simulation using OPM (Open Porous Media Initiative) Flow 

Duration: 60 minutes

### Task 1: Build OPM

1. On the lab computer, in the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteradmin@hb120v3-1 ~]$**, run the following command to load Spack modules

   ```bash
   module use /usr/share/Modules/modulefiles
   ```

1. Run the following command to create the az-hop Spack repo:

   ```bash
   ~/azurehpc/experimental/azhop/azhop-spack/install.sh
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   ==> Added repo with namespace 'azhop'.
   ```

1. Run the following command to configure the OPM packages:

   ```bash
   ~/azurehpc/experimental/azhop/opm/configure.sh
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hb120v3-1 ~]$ ~/azurehpc/experimental/azhop/opm/configure.sh
   Cloning into 'dune-spack'...
   remote: Enumerating objects: 357, done.
   remote: Total 357 (delta 0), reused 0 (delta 0), pack-reused 357
   Receiving objects: 100% (357/357), 74.34 KiB | 0 bytes/s, done.
   Resolving deltas: 100% (179/179), done.
   ==> Added repo with namespace 'dune'.
   ```

1. Run the following command to list available modules:

   ```bash
   module use /usr/share/Modules/modulefiles
   module avail
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hb120v3-1 .spack]$ module avail

   ---------------------------------------- /usr/share/Modules/modulefiles ----------------------------------------
      amd/aocl              module-git         mpi/hpcx               mpi/impi-2021         mpi/openmpi-4.1.0 (D)
      amd/aocl-2.2-4 (D)    module-info        mpi/impi_2018.4.274    mpi/mvapich2          null
      dot                   modules            mpi/impi_2021.2.0      mpi/mvapich2-2.3.5    use.own
      gcc-9.2.0             mpi/hpcx-v2.8.3    mpi/impi               mpi/openmpi

   ------------------------------------ /usr/share/lmod/lmod/modulefiles/Core -------------------------------------
      lmod    settarg

     Where:
      D:  Default Module

   Use "module spider" to find all possible modules and extensions.
   Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
   ```

   > ![Note]: Review the output and note the openmpi version.

1. Use your preferred text editor to update the content of the **~/.spack/packages.yaml** by replacing references to **hpcx** with **openmpi**, removing **hpcx** related entries, and, if necessary, updating the openmpi version to the one you identified in the previous step.

   > ![Note]: The following is a sample content of the **~/.spack/packages.yaml** file:

   ```bash
   packages:
     all:
       target: [x86_64]
       providers: 
         mpi: [hpcx]
     openmpi:
       externals:
       - spec: openmpi@4.0.5%gcc@9.2.0
         modules:
         - mpi/openmpi-4.0.5
       buildable: False
     hpcx:
       externals:
       - spec: hpcx@2.7.4%gcc@9.2.0
         modules:
         - mpi/hpcx-v2.7.4
       buildable: False
   ```

   > ![Note]: The following is the updated content to be used with openmpi version 4.1.0.

   ```bash
   packages:
     all:
       target: [x86_64]
       providers: 
         mpi: [openpmi]
     openmpi:
       externals:
       - spec: openmpi@4.1.0%gcc@9.2.0
         modules:
         - mpi/openmpi-4.1.0
       buildable: False
   ```

1. Run the following command to initialize the OPM build:

   ```bash
   ~/azurehpc/experimental/azhop/opm/build.sh
   ```

   > ![Note]: You should see an output that starts with the following listing:

   ```bash
   [clusteradmin@hb120v3-1 ~]$ ~/azurehpc/experimental/azhop/opm/build.sh
   ==> Warning: Missing a source id for openmpi@4.1.0
   ==> Warning: Missing a source id for dune@2.7
   ==> Installing pkg-config-0.29.2-opt4cajmlefjsbaqmhcuxegkkdr6gvac
   ==> No binary for pkg-config-0.29.2-opt4cajmlefjsbaqmhcuxegkkdr6gvac found: installing from source
   ==> Fetching https://mirror.spack.io/_source-cache/archive/6f/6fc69c01688c9458a57eb9a1664c9aba372ccda420a02bf4429fe610e7e7d591.tar.gz
   ######################################################################## 100.0%
   ==> pkg-config: Executing phase: 'autoreconf'
   ==> pkg-config: Executing phase: 'configure'
   ```

   > ![Note]: Wait for the build to complete. This might take about 30 minutes.

### Task 2: Retrieve test data and run a flow job

1. On the lab computer, in the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteradmin@hb120v3-1 ~]$**, run the following commands to download test data:

   ```bash
   cd /lustre
   mkdir clusteradmin
   cd clusteradmin
   git clone https://github.com/OPM/opm-data.git
   ```

1. Run the following command to copy the **~/azurehpc/experimental/azhop/opm/run_opm.sh** file to the home directory:

   ```bash
   cp ~/azurehpc/experimental/azhop/opm/run_opm.sh ~
   ```

1. On the lab computer, in the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteradmin@hb120v3-1 ~]$**, open the newly created copy of the **run_opm.sh** file in a text editor, make the following changes, and close it saving the changes:

   - modify the input file path (`INPUT`) by replacing the entry `~/opm-data/norne` with `/lustre/clusteradmin/opm-data/norne`)
   - modify the compute node configuration by replacing the entry `select=1:ncpus=40:mpiprocs=40:slot_type=hc44rs` with `select=1:ncpus=120:mpiprocs=60:slot_type=hb120v3`
   - add the `which flow` line following the `spack load opm-simulators` line.

   > ![Note]: Leave the number of nodes set to 1 to avoid the quota limit issues.

   > ![Note]: The original **run_opm.sh** file has the following content:

   ```
   #!/bin/bash
   #PBS -N OPM
   #PBS -l select=1:ncpus=40:mpiprocs=40:slot_type=hc44rs
   #PBS -k oed
   #PBS -j oe
   #PBS -l walltime=3600

   INPUT=~/opm-data/norne/NORNE_ATW2013.DATA
   INPUT_DIR=${INPUT%/*}
   INPUT_FILE=${INPUT##*/}
   NUM_THREADS=1

   . ~/spack/share/spack/setup-env.sh
   module use /usr/share/Modules/modulefiles
   spack load opm-simulators

   pushd $INPUT_DIR
   CORES=`cat $PBS_NODEFILE | wc -l`

   mpirun  -np $CORES \
           -hostfile $PBS_NODEFILE \
           --map-by numa:PE=$NUM_THREADS \
           --bind-to core \
           --report-bindings \
           --display-allocation \
           -x LD_LIBRARY_PATH \
           -x PATH \
           -wd $PWD \
           flow --ecl-deck-file-name=$INPUT_FILE \
                --output-dir=$INPUT_DIR/out_parallel \
                --output-mode=none \
                --output-interval=10000 \
                --threads-per-process=$NUM_THREADS
   popd
   ```

   > ![Note]: The modified **run_opm.sh** file should have the following content:

   ```
   #!/bin/bash
   #PBS -N OPM
   #PBS -l select=1:ncpus=120:mpiprocs=60:slot_type=hb120v3
   #PBS -k oed
   #PBS -j oe
   #PBS -l walltime=3600

   INPUT=/lustre/clusteradmin/opm-data/norne/NORNE_ATW2013.DATA
   INPUT_DIR=${INPUT%/*}
   INPUT_FILE=${INPUT##*/}
   NUM_THREADS=1

   . ~/spack/share/spack/setup-env.sh
   module use /usr/share/Modules/modulefiles
   spack load opm-simulators
   which flow

   pushd $INPUT_DIR
   CORES=`cat $PBS_NODEFILE | wc -l`

   mpirun  -np $CORES \
           -hostfile $PBS_NODEFILE \
           --map-by numa:PE=$NUM_THREADS \
           --bind-to core \
           --report-bindings \
           --display-allocation \
           -x LD_LIBRARY_PATH \
           -x PATH \
           -wd $PWD \
           flow --ecl-deck-file-name=$INPUT_FILE \
                --output-dir=$INPUT_DIR/out_parallel \
                --output-mode=none \
                --output-interval=10000 \
                --threads-per-process=$NUM_THREADS
   popd
   ```

1. On the lab computer, in the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteradmin@hb120v3-1 ~]$**, run the following command to submit the job referenced in the **~/run_opm.sh** file:

   ```bash
   qsub ~/run_opm.sh 
   ```

1. Run the following command to verify that the job has been queued:

   ```bash
   qstat
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hb120v3-1 clusteradmin]$ qsub ~/run_opm.sh
   8.scheduler
   [clusteradmin@hb120v3-1 clusteradmin]$ qstat
   Job id            Name             User              Time Use S Queue
   ----------------  ---------------- ----------------  -------- - -----
   6.scheduler       sys-dashboard-s  clusteradmin      00:00:17 R workq           
   7.scheduler       STDIN            clusteradmin      03:24:08 R workq           
   8.scheduler       OPM              clusteradmin             0 Q workq  
   ```

   > ![Note]: Review the output to identify the job name. 

1. In the output of the previous command, identify the **Job id** of the newly submitted job and run the following command to display its status (replace the `<jobid>` placeholder with the value you identified):

   ```bash
   qstat -fx <jobid>
   ```

   > ![Note]: You should see an output that starts with the following listing:

   ```bash
   [clusteradmin@hb120v3-1 clusteradmin]$ qstat -fx 8.scheduler
   Job Id: 8.scheduler
       Job_Name = OPM
       Job_Owner = clusteradmin@hb120v3-1.internal.cloudapp.net
       resources_used.cpupercent = 1635
       resources_used.cput = 00:45:00
       resources_used.mem = 29380680kb
       resources_used.ncpus = 120
       resources_used.vmem = 117657196kb
       resources_used.walltime = 00:00:55
       job_state = F
       queue = workq
       server = scheduler
       Checkpoint = u
       ctime = Tue Feb 22 18:26:47 2022
       Error_Path = hb120v3-1.internal.cloudapp.net:/lustre/clusteradmin/OPM.e8
       exec_host = hb120v3-2/0*120
       exec_vnode = (hb120v3-2:ncpus=120)
       Hold_Types = n
       Join_Path = oe
       Keep_Files = oed
       Mail_Points = a
       mtime = Tue Feb 22 18:36:42 2022
       Output_Path = hb120v3-1.internal.cloudapp.net:/lustre/clusteradmin/OPM.o8
       Priority = 0
       qtime = Tue Feb 22 18:26:47 2022
       Rerunable = True
       Resource_List.mpiprocs = 60
       Resource_List.ncpus = 120
       Resource_List.nodect = 1
       Resource_List.place = scatter:excl
       Resource_List.select = 1:ncpus=120:mpiprocs=60:slot_type=hb120v3
       Resource_List.slot_type = execute
       Resource_List.ungrouped = false
       Resource_List.walltime = 01:00:00
       stime = Tue Feb 22 18:35:47 2022
       session_id = 12364
       jobdir = /anfhome/clusteradmin
       substate = 92
       Variable_List = PBS_O_HOME=/anfhome/clusteradmin,PBS_O_LANG=en_US.UTF-8,
           PBS_O_LOGNAME=clusteradmin,
           PBS_O_PATH=/anfhome/clusteradmin/spack/bin:/bin:/usr/bin:/usr/local/sb
           in:/usr/sbin:/opt/cycle/jetpack/bin:/opt/pbs/bin:/anfhome/clusteradmin/
           .local/bin:/anfhome/clusteradmin/bin,
           PBS_O_MAIL=/var/spool/mail/clusteradmin,PBS_O_SHELL=/bin/bash,
           PBS_O_WORKDIR=/lustre/clusteradmin,PBS_O_SYSTEM=Linux,
           PBS_O_QUEUE=workq,PBS_O_HOST=hb120v3-1.internal.cloudapp.net
       comment = Not Running: Not enough free nodes available
       etime = Tue Feb 22 18:26:47 2022
       run_count = 1
       Stageout_status = 1
       Exit_status = 0
       Submit_arguments = /anfhome/clusteradmin/run_opm.sh
       history_timestamp = 1645555002
       project = _pbs_project_default
   ```

   > ![Note]: Wait for the job to complete. This might take about 5 minutes.

   > ![Note]: To verify the completion of the job, you can rerun the `qstat` command or the `qstat -fx <jobid>` command. The latter, should display the comment in the format `comment = Job run at Tue Feb 22 at 18:40 on (hb120v3-2:ncpus=120) and finished`

1. Run the following command to verify that the job has generated the expected output:

   ```bash
   ls /lustre/clusteradmin/opm-data/norne/out_parallel/
   ```

   > ![Note]: Alternatively, you can review the content of the corresponding the job-related `~/OPM.*` file

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteradmin@hb120v3-1 clusteradmin]$ ls /lustre/clusteradmin/opm-data/norne/out_parallel/
   NORNE_ATW2013.EGRID     NORNE_ATW2013.INIT  NORNE_ATW2013.SMSPEC  NORNE_ATW2013.UNSMRY
   NORNE_ATW2013.INFOSTEP  NORNE_ATW2013.RFT   NORNE_ATW2013.UNRST
   ```

### Task 3: View the results of the OPM job by using ResInsight

1. On the lab computer, in the browser window, switch back to the **Azure HPC On-Demand Platform** portal and, in the **Interactive Apps** section, select **Remote Desktop**.
1. On the **Remote Desktop** launching page, in the **Session target** drop-down list, ensure that **With GPU** entry is selected, in the **Maximum duration of your remote session** list, enter **1**,  and then select **Launch**.

   > ![Note]: In case your subscription does not have sufficient number of quotas for the **Standard_NV6** SKU, choose the **Without GPU** entry instead.

   > ![Note]: This will initiate provisioning of a compute node of the type you specified. Note that this creates a new job, which **Queued** status is displayed on the same page.

1. Switch back to the **Remote Desktop** launching page, verify that the status of the corresponding job has changed to **Running**, adjust **Compression** and **Image quality** according to your preferences, and select **Launch Remote Desktop**.

   > ![Note]: This will open another browser tab displaying the Remote Desktop session.

1. Within the Remote Desktop session, start **Terminal Emulator**.

   > ![Note]: ResInsight installation instructions are available at (https://resinsight.org/getting-started/download-and-install/linux-installation/)[https://resinsight.org/getting-started/download-and-install/linux-installation/]

1. In the **Terminal Emulator** window, at the **[clusteradmin@vis-1 ~]$** prompt, run the following commands to sudo to root:

   ```bash
   sudo su -
   ```

1. In the **Terminal Emulator** window, use the vi editor to open the **/etc/yum.conf** file, add a new line with the `sslverify=0` entry, save the change, and close the file.

   > ![Note]: This is necessary in order to prevent the **Peer's Certificate issuer is not recognized** error message when downloading the ResInsight package.

1. In the **Terminal Emulator** window, at the **[clusteradmin@vis-1 ~]$** prompt, run the following command to install the ResInsight package:

   ```bash
   yum-config-manager --add-repo https://opm-project.org/package/opm.repo
   yum install resinsight -y
   yum install resinsight-octave -y
   ```

1. In the **Terminal Emulator** window, at the **[clusteradmin@vis-1 ~]$** prompt, run the following command to launch ResInsight:

   ```bash
   exit
   vglrun ResInsight
   ```

   > ![Note]: This will open the ResInsight application window.

   > ![Note]: In case you are using a non-GPU VM SKU, rather than running `vglrun ResInsight`, in the **Remote Desktop** session, select **Application Finder** (the magnifying glass icon at the bottom center section of the page), in the search text box, type **ResInsight**, in the list of search results, select **ResInsight**, and then select **Launch**.

1. In the ResInsight application window, select the **File** menu header, in the drop-down menu, select **Import**, in the cascading menu, select **Eclipse cases** and then select **Import Eclipse cases**.
1. In the **Import Eclipse File** dialog box, select **Computer**, then navigate to **/lustre/clusteradmin/opm-data/norne/out_parallel** and, in the list of files in the target directory, select **NORNE_ATW2013.EGRID**.
1. Review the case in the ResInsight application window.

## Exercise 7: Deprovision Azure HPC OnDemand Platform environment

Duration: 30 minutes

In this exercise, you will deprovision the Azure HPC OnDemand Platform lab environment.

### Task 1: Terminate the cluster

1. On the lab computer, in the browser window, navigate to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal.
1. On the **pbs1** page, select **Terminate** and, when prompted for confirmation, select **OK**.
1. Monitor the progress of terminating the cluster.

   > ![Note]: Ensure that all nodes and scaleset are deleted before you proceed to the next step.


