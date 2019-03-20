# Azure Migration  Lab
## Azure Site Recovery for Migration  
 
In this lab you will create a VM in Azure to simulate a source VM running in either VMware or Hyper-V on the ground.  We will then replicate (aka migrate) the VM to Azure.

Please note that using this approach represents `the fastest way` to migrate a VM to Azure and should not be seen as the usual, customary amount of time it takes to perform a migration to Azure. 

### Create a Virtual Machine
We need to create a source to migrate.  This would normally be a physical or virtual machine on the ground, but for lab purposes we are going to perform an Azure to Azure migration.  Conceptually the process is identical and less time consuming since we leverage the power of Azure compute, network, and storage resources.
1.	Select **+ Create a resource** found on the upper left corner of the Azure portal.
2.	Select **Compute**, and then select **Windows Server 2016 Datacenter**.
3.	Enter or select the following information, accept the defaults for the remaining settings:
    * Resource Group: *Create New*  **Migration**
    * Virtual Machine Name: SourceVM
    * Region: East US
    * Size: Standard **B2ms** 
    * Username: pick a username and write it down
    * Password: `Complex.Password` and write it down
    * Confirm Password:  `Complex.Password` and write it down
    * Public inbound ports:  Open **RDP**
4.	Click **Review + create**.
5.	Once validation passes click **Create**.

### Create a Recovery Services vault
1. Click **Create a resource > Management Tools > Backup and Site Recovery(OMS)** and enter **MyVault** as the Name and **Migration** as the Resource Group.  Click **Create**.

### Select a replication goal
1. In the search bar type in **MyVault** and select it.
2. In the **Getting Started** Menu, click **Site Recovery** > **Prepare Infrastructure** . In **Protection goal**, set the following: 
    * Where are your machines located?: **Azure** 
    * Where do you want to replicate your machines to? **To Azure**
    * Click **OK**, and then **OK** again on the **Prepare Infrastructure** tab.

#### Enable replication
1.	In the Azure portal, click **Virtual machines**, and select the **SourceVM**. 
2.	In **Operations**, click **Disaster recovery**.
3.	In **Configure disaster recovery** > **Target region** select the target region to which you'll replicate.
4.	Review the  settings and click **Next: Advanced settings**.
5. This is the configuration blade where you would specify your target Subscription, Resource Group, Virtual Network, and your HA configuration.  For lab purposes we'll use the defaults and click **Next: Review + Start Replication**.
6. Review the settings and click **Start replication**. This starts a job to enable replication for the VM.
7.	You may notice that Vaildating takes a few moments to process.  The fabric is ensuring that resources in your target region can be created and there’s no conflicts.
8. You can click on the **deployment progress** in your alerts window to monitor status.


#### Track Replication
1. Once Azure has built the core components replication will begin.  On the alert button (the bell) click on **Enabling replication for 1 vm(s)**.
2.	Notice the steps as they occur in real time.  The longest step in the process is going to be **Enable replication**.  Select that item and observe the series of steps taking place. IR, or Initial Replication, the time it takes the VM to be copied from source to target.  Notice the Status of IR.  
2.	Since it may take several hours to replicate the VM, now may be an appropriate time to take a break or come back to the lab at a later time.
3.	You can check percentage complete of replication by **Virtual Machines > SourceVM > Operations > Disaster Recovery**.  You may notice status sits at 0% synchronized for some time and then report upwards of 87% complete on next refresh.

## Make it Real
In production, once your VM is replicated to Azure, your next step would be to establish the cut-over process from ground to cloud resources. There are several ways to accomplish this:

1. Enable SQL replication between on-prem SQL and the Azure SQL database allowing users to read/write to/from on-prem SQL server and then use SQL replication to keep the data in sync. Once you're confident about data consistency, you can move on to step 2.
2. Update your clients to point to Azure SQL.  There are too many variations to discuss one single approach, however common approaches include updating connection string, putting a load balalnce in front of the SQL servers, and many others.
 

