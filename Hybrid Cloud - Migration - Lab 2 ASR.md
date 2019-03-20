# Migration Azure Lab
## Azure Site Recovery for Migration  
 
In this lab you will create a VM in Azure to simulate a source VM running in either VMware or Hyper-V onthe ground.  We will then replicate (aka migrate) the VM to Azure.

### Create a Virtual Machine
1.	Select **+ Create a resource** found on the upper, left corner of the Azure portal.
2.	Select Compute, and then select Windows Server 2016 Datacenter.
3.	Enter, or select, the following information, accept the defaults for the remaining settings:
    * Resource Group: VMDR
    * Virtual Machine Name: IaaSVM
    * Region: East US
    * Size: Standard Ds2 v2 (DS2_v2)
    * Username: pick a username
    * Password: pick a complex password
    * Confirm Password: pick a complex password
    * Public inbound ports:  Open RDP
    * Select Next: Disks >
4.	Click Next: Networking.
5.	Select Next: Management.
6.	Use the previously created diagnostic storage account and then click Next: Guest config.
7.	Review the items and then click Next: Tags.
8.	Review the items and then click Next: Review + create.
9.	Once validation passes click Create.

 
Enable replication
1.	In the Azure portal, click Virtual machines, and select the IaaSVM. 
2.	In Operations, click Disaster recovery.
3.	In Configure disaster recovery > Target region select the target region to which you'll replicate.
4.	Review the default settings and click Enable replication. This starts a job to enable replication for the VM.
5.	You may notice that Vaildating takes a few moments to process.  The fabric is ensuring that resources in your target region can be created and there’s no conflicts.
Track Replication
On the alert button (the bell) click on Enabling replication for 1 vm(s).
1.	Notice the steps as they occur in real time.  The longest step in the process in going to be Enable replication.  Select that item and observe the series of steps taking place. IR, or Initial Replication, the time it takes the VM to be copied from source to target.  Notice the Status of IR.  
2.	Since it may take 30 minutes to replicate the VM, now may be an appropriate time to take a break.
3.	You can check percentage complete or replication by Virtual Machines -> IaaSVM -> Disaster Recovery.  You may notice status sits at 0% synchronized for some time and then report upwards of 87% complete on next refresh.
4.	When all tasks are complete, you should see a similar status:
 
Until the VM is 100% synchronized and Protected, a test failover is not possible.  It may take several hours for your VM to replicate in a production environment.
