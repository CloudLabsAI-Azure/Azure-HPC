![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Azure HPC OnDemand Platform
</div>

<div class="MCWHeader2">
Hands-on lab guide
</div>

<div class="MCWHeader3">
February 2022
</div>

<!-- TOC -->

## Exercise 5: Set up Spack 

Duration: 30 minutes

In this exercise, you will install and configure Spack from Code Server, as documented in (https://azure.github.io/az-hop/tutorials/spack.html)[https://azure.github.io/az-hop/tutorials/spack.html]

### Task 1: Create a compute node

1. Open a new tab in your browser, navigate to the **FQDN** of the **Azure HPC On-Demand Platform portal** which is provided in the lab environment details page.

     ![](../main/media/ondemandsite.png)
     
1. When you are prompted to sign in use the credentials provided in the lab environment details page:

    - **Username**: Enter the **HPC Username** provided in your environment details page. 
    - **Password** : Enter the **HPC Password** provided in your environment details page.

   > **Note**: You will be presented with the **Azure HPC On-Demand Platform** dashboard. Review its interface, starting with the top level menu, which provides access to **Apps**, **Files**, **Jobs**, **Clusters**, **Interactive Apps**, **Monitoring**, and **My Interactive Sessions** menu items.

1. In the **Azure HPC On-Demand Platform** portal, select the **Interactive Apps** menu, and in the drop-down menu, select **Code Server**.

   > ![Note]: This will open another browser tab displaying the Code Server launching page.

1. On the **Code Server** launching page, in the **Maximum duration of your remote session** list, enter **3**, in the **Slot Type** text box, type **execute**, and then select **Launch**.

   > ![Note]: This will initiate provisioning of a compute node of the type you specified. Note that this creates a new job, which **Queued** status is displayed on the same page.

1. Switch to the **Azure CycleCloud for Azure HPC On-Demand Platform** portal and monitor the progress of the **execute** node provisioning.

   > ![Note]: Wait until the status of the node changes to **Ready**. This should take about five minutes.

1. Switch back to the **Code Server** launching page, verify that the status of the corresponding job has changed to **Running** and select **Connect to VS Code**.

   > ![Note]: This will open another browser tab displaying the Code Server interface.

1. Review the interface, close the **Welcome** tab, select the **Application menu** in the top left corner of the page, in the drop-down menu, select **Terminal** and then, in the cascading menu, select **New Terminal**.
1. In the Terminal pane, at the **[clusteruserX@execute-X ~]$** prompt, run the following command to clone the azurehpc repo and use the azhop/spack branch:

   ```bash
   git clone https://github.com/Azure/azurehpc.git
   ```

### Task 2: Install Spack

1. In the browser window displaying the Code Server, wait until the prompt **[clusteruserX@execute-X ~]$** appears within the Terminal pane.
1. In the Terminal pane, run the following commands to install and configure Spack:

   ```bash
   ~/azurehpc/experimental/azhop/spack/install.sh
   ~/azurehpc/experimental/azhop/spack/configure.sh
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteruserX@execute-X ~]$ ~/azurehpc/experimental/azhop/spack/install.sh
   Cloning into '/anfhome/clusteruserX/spack'...
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
   [clusteruserX@execute-X ~]$ ~/azurehpc/experimental/azhop/spack/configure.sh
   Add GCC compiler
   ==> Added 1 new compiler to /anfhome/clusteradmin/.spack/linux/compilers.yaml
       gcc@9.2.0
   ==> Compilers are defined in the following files:
       /anfhome/clusteruserX/.spack/linux/compilers.yaml
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
   [clusteruserX@execute-X ~]$ . ~/spack/share/spack/setup-env.sh
   [clusteruserX@execute-X ~]$ spack compilers
   ==> Available compilers
   -- gcc centos7-x86_64 -------------------------------------------
   gcc@9.2.0
   ```

   > ![Note]: Verify that gcc 9.2 is listed.

## Exercise 6: Build, run, and analyze reservoir simulation using OPM Flow

In this exercise, you will build, run, and analyze reservoir simulation using OPM (Open Porous Media Initiative) Flow 

Duration: 60 minutes

### Task 1: Build OPM

1. In the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteruserX@execute-X ~]$**, run the following command to load Spack modules

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
   [clusteruserX@execute-X ~]$ ~/azurehpc/experimental/azhop/opm/configure.sh
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
   [clusteruserX@execute-X .spack]$ module avail

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
   [clusteruserX@execute-X ~]$ ~/azurehpc/experimental/azhop/opm/build.sh
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

1. In the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteruserX@execute-X ~]$**, run the following commands to download test data:

   ```bash
   cd /lustre
   mkdir $USER
   cd $USER
   git clone https://github.com/OPM/opm-data.git
   ```

1. Run the following command to copy the **~/azurehpc/experimental/azhop/opm/run_opm.sh** file to the home directory:

   ```bash
   cp ~/azurehpc/experimental/azhop/opm/run_opm.sh ~
   ```

1. In the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteruserX@execute-X ~]$**, open the newly created copy of the **run_opm.sh** file in a text editor, make the following changes, and close it saving the changes:

   - modify the input file path (`INPUT`) by replacing the entry `~/opm-data/norne` with `/lustre/clusteradmin/opm-data/norne`)
   - modify the compute node configuration by replacing the entry `select=1:ncpus=40:mpiprocs=40:slot_type=hc44rs` with `select=1:ncpus=120:mpiprocs=60:slot_type=hb120v2`
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
   #PBS -l select=1:ncpus=120:mpiprocs=60:slot_type=hb120v2
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

1. In the browser window displaying the Code Server, in the Terminal pane, from the prompt **[clusteruserX@execute-X ~]$**, run the following command to submit the job referenced in the **~/run_opm.sh** file:

   ```bash
   qsub ~/run_opm.sh 
   ```

1. Run the following command to verify that the job has been queued:

   ```bash
   qstat
   ```

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteruserX@execute-X clusteruserX]$ qsub ~/run_opm.sh
   8.scheduler
   [clusteruserX@execute-X clusteruserX]$ qstat
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
   [clusteruserX@execute-X clusteruserX]$ qstat -fx 8.scheduler
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
       Error_Path = hb120v2-1.internal.cloudapp.net:/lustre/clusteruserX/OPM.e8
       exec_host = hb120v2-2/0*120
       exec_vnode = (hb120v2-2:ncpus=120)
       Hold_Types = n
       Join_Path = oe
       Keep_Files = oed
       Mail_Points = a
       mtime = Tue Feb 22 18:36:42 2022
       Output_Path = hb120v2-1.internal.cloudapp.net:/lustre/clusteruserX/OPM.o8
       Priority = 0
       qtime = Tue Feb 22 18:26:47 2022
       Rerunable = True
       Resource_List.mpiprocs = 60
       Resource_List.ncpus = 120
       Resource_List.nodect = 1
       Resource_List.place = scatter:excl
       Resource_List.select = 1:ncpus=120:mpiprocs=60:slot_type=hb120v2
       Resource_List.slot_type = execute
       Resource_List.ungrouped = false
       Resource_List.walltime = 01:00:00
       stime = Tue Feb 22 18:35:47 2022
       session_id = 12364
       jobdir = /anfhome/clusteruserX
       substate = 92
       Variable_List = PBS_O_HOME=/anfhome/clusteruserX,PBS_O_LANG=en_US.UTF-8,
           PBS_O_LOGNAME=clusteruserX,
           PBS_O_PATH=/anfhome/clusteruserX/spack/bin:/bin:/usr/bin:/usr/local/sb
           in:/usr/sbin:/opt/cycle/jetpack/bin:/opt/pbs/bin:/anfhome/clusteruserX/
           .local/bin:/anfhome/clusteruserX/bin,
           PBS_O_MAIL=/var/spool/mail/clusteruserX,PBS_O_SHELL=/bin/bash,
           PBS_O_WORKDIR=/lustre/clusteruserX,PBS_O_SYSTEM=Linux,
           PBS_O_QUEUE=workq,PBS_O_HOST=hb120v3-1.internal.cloudapp.net
       comment = Not Running: Not enough free nodes available
       etime = Tue Feb 22 18:26:47 2022
       run_count = 1
       Stageout_status = 1
       Exit_status = 0
       Submit_arguments = /anfhome/clusteruserX/run_opm.sh
       history_timestamp = 1645555002
       project = _pbs_project_default
   ```

   > ![Note]: Wait for the job to complete. This might take about 5 minutes.

   > ![Note]: To verify the completion of the job, you can rerun the `qstat` command or the `qstat -fx <jobid>` command. The latter, should display the comment in the format `comment = Job run at Tue Feb 22 at 18:40 on (hb120v3-2:ncpus=120) and finished`

1. Run the following command to verify that the job has generated the expected output:

   ```bash
   ls /lustre/clusteruserX/opm-data/norne/out_parallel/
   ```

   > ![Note]: Alternatively, you can review the content of the corresponding the job-related `~/OPM.*` file

   > ![Note]: You should see an output that resembles the following listing:

   ```bash
   [clusteruserX@execute-X clusteruserX]$ ls /lustre/clusteruserX/opm-data/norne/out_parallel/
   NORNE_ATW2013.EGRID     NORNE_ATW2013.INIT  NORNE_ATW2013.SMSPEC  NORNE_ATW2013.UNSMRY
   NORNE_ATW2013.INFOSTEP  NORNE_ATW2013.RFT   NORNE_ATW2013.UNRST
   ```

### Task 3: View the results of the OPM job by using ResInsight

1. In the browser window, switch back to the **Azure HPC On-Demand Platform** portal and, in the **Interactive Apps** section, select **Remote Desktop**.
1. On the **Remote Desktop** launching page, in the **Session target** drop-down list, ensure that **With GPU** entry is selected, in the **Maximum duration of your remote session** list, enter **1**,  and then select **Launch**.

   > ![Note]: In your subscription, you does not have sufficient number of quotas for the **Standard_NV6** SKU, so please choose the **Without GPU** entry instead.

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



