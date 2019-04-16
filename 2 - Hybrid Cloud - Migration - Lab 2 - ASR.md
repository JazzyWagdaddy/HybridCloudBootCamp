# Azure Migration Lab #2
## Azure Site Recovery for Migration  
 
In this lab you will create a VM in Azure to simulate a source VM running in either VMware or Hyper-V on the ground.  We will then replicate (aka migrate) the VM to Azure.

Please note that using this approach represents `the fastest way` to migrate a VM to Azure and should not be seen as the usual, customary amount of time it takes to perform a migration to Azure. 


## Task 1 - Create an IIS VM with PowerShell

This script creates an Azure Virtual Machine running Windows Server 2016, and then uses the Azure Virtual Machine DSC Extension to install IIS. After running the script, you can access the default IIS website on the public IP address of the virtual machine.

This lab requires Azure PowerShell.

1. This lab requires Azure PowerShell.  If you need to install Azure PowerShell, see [Install Azure PowerShell module](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps).

1. Open PowerShell and Run `Connect-AzAccount` to create a logon to your Azure subscription.
2. Open a command prompt and map the z: to an Azure files share:

`net use Z: \\wagsazurefiles.file.core.windows.net\buildiis /persistent:Yes`

*Note that if the drive letter Z: is already used on your local computer feel free to change I: to any available drive letter.*

3. Copy the build-iis-vm.ps1 to your local computer.  Feel free to open up the PowerShell ISE and examine the script.

`copy z:\build-iis-vm.ps1 c:\users\yourprofile\downloads`

4. From PowerShell execute the build-iis-vm.ps1 script from your Downloads directory:

`.\Build-IIS-VM.ps1`

5. Enter the username and password for the IIS VM:
    * Username:  pick a username and notate the credentials
    * Password: Enter `Complex.Password` and notate the credentials 
6. Observe the build process via PowerShell.
7. Once PowerShell builds the VM and installs IIS, open the Azure Portal and then obtain the public IP address of the IIS virtual machine.
8. Open a web browser and surf to the public IP address just make sure things are working.

## Task 2 - Create target network resource
1. Click on Virtual networks then **+Add**
2. 	Enter or select the following information, accept the defaults for the remaining settings, and then select **Create**:
    * Name: **MigrationvNet**
    * Address Space: **10.10.0.0/16**
    * Resource Group: *Create New* **MigrationvNets**
    * Location: **Central US****
    * Subnet Name: **migsub**
    * Subnet address range: **10.10.10.0/24** 

## Task 3 - Create a Recovery Services vault
This task is the normal starting point for a typical lift and shift migration as you would normally have a robust source environment to migrate. 
1. Click **Create a resource > Management Tools > Backup and Site Recovery(OMS)** and enter **MyVault** as the Name and **Migration** as the Resource Group.  Click **Create**.


## Task 4 - Select a replication goal
1. Once the deployment has succeeded, click on **Go to Resource** or in the search bar type in **MyVault** and select it.
2. In the **Getting Started** Menu, click **Site Recovery** > **Prepare Infrastructure**. 
3. In **Protection goal**, set the following: 
    * Where are your machines located?: **Azure** 
    * Where do you want to replicate your machines to? **To Azure**
    * Click **OK**, and then **OK** again on the **Prepare Infrastructure** tab. 

## Task 5 - Enable replication
1.	In the Azure portal, click **Virtual machines**, and select the **IIS**. 
2.	Under **Operations**, click **Disaster recovery**.
3.	In **Configure disaster recovery** > **Target region** select the target region to which you'll replicate and where you create the network resources in Task 2, which should be the Central US. Click **Next: Advanced settings**.
4. Under Advanced settings, set the following and click **Next: Review + Start Replication**.
    * VM resource group: **MigrationvNets**
    * Virtual network" **MigrationvNet**
    * Availability: **Availability set**
5. Click on **Review + Start replication**.
6. Review the settings and click **Start replication**. This starts a job to enable replication (aka migration) for the VM.
7.	You may notice that Vaildating takes a few moments to process.  The fabric is ensuring that resources in your target region can be created and there’s no conflicts.


## Task 5 - Track Replication
1. Once Azure has built the core components replication will begin.  On the alert button (the bell) click on **Enabling replication for 1 vm(s)**.
2.	Notice the steps as they occur in real time.  The longest step in the process is going to be **Enable replication**.  Select that item and observe the series of steps taking place. IR, or Initial Replication, the time it takes the VM to be copied from source to target.  Notice the Status of IR.  
2.	Since it may take 30 minutes to replicate the VM, now may be an appropriate time to take a break or come back to the lab at a later time.
3.	You can check percentage complete of replication by **Virtual Machines > IIS > Operations > Disaster Recovery**.  You may notice status sits at 0% synchronized for some time and then report upwards of 87% complete on next refresh.


## Task 6 - Run a Test Failover 
A test failover executes a failover but does not make the secondary or migrated VM active.  A drill validates your replication strategy without data loss or downtime and doesn't affect your production environment.
1.	Click the **Test Failover** icon.
2.	In Test Failover, select Latest (lowest RPO) as the recovery point to use for the failover.  Note the following:
    * **Latest (lowest RPO):** Fails the VM over with the current state of the VM but requires some processing time.
    * **Latest processed (low RTO):** Fails the VM over to the latest recovery point that was processed by the Site Recovery service. The time stamp is shown. With this option, no time is spent processing data, so it provides a low RTO (Recovery Time Objective)
    * **Latest app-consistent:** This option fails over all VMs to the latest app-consistent recovery point. The time stamp is shown.
    * **Custom:** Use this option to fail over to a specific recovery point. This option is useful for performing a test failover.
3.	Select the target Azure virtual network to which Azure VMs in the secondary region will be connected after the failover occurs.  
4.	To start the failover, click **OK**. Track progress by selecting the alert in the Notifications window. 
5.	After the failover finishes (Start the virtual machine task is successful), the replica Azure VM appears in the Azure portal under Virtual Machines. Make sure that the VM is running, sized appropriately, and connected to the appropriate network. Note that the VM does not have a Public IP address.
6.	To delete the VMs that were created during the test failover, select **IIS-test** from **Virtual Machines**, select **Disaster recovery** under  **Operations**, and then choose **Cleanup test failover**. In Notes, record and save any observations associated with the test failover. Click the box for **Testing is complete** and click **Ok**.
If you don’t delete the failover VM, the VM will continue to run and increase your Azure consumption.

 ## Task 6 - Switch over to the migrated VM
 Once you have validated the migrated VM by performing a test failover, your next step will be to ensure that the VM works as planned.  In this lab you will shutdown the source VM and then surf to the migrated VM to ensure IIS still renders.

 1.  
