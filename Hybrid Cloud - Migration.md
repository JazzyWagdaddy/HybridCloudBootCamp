# Azure Migration

## Database Migration Service
In this lab you are going to create an IaaS VM with SQL as a source environment, load the database up with data, then use DMS to migrate that over to Azure.

### Create a SQL Server VM image
1. Log in to the Azure portal using your account.
2. On the Azure portal, click **Create a resource**.
3. In the search field, type **SQL Server 2017 Developer on Windows Server 2016**, and press **ENTER**.
4. Select the **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016 image** and click **Create**.
5. Enter the following and then click **OK**.
    * Name: **SQLVM**
    * Username: *pick one*
    * Password: *Complex.Password*
    * Confirm Password: *Complex.Password*
    * Resource Group: *create new* **SQLMIG**
6. Choose **D2S_V3** as the size and click **Select**.
7. On the **Configure optional features** blade select **RDP** under **Select public inbound ports** and click **OK**.
8. On the **Configure SQL Server settings** blade complete the following:
    * In the SQL connectivity drop-down, select **Public (Internet)**. This allows SQL Server connections over the internet.
    * Change the Port to **1401** to avoid using a well-known port name in the public scenario.
    * Under SQL Authentication, click **Enable**. The SQL Login is set to the same user name and password that you configured for the VM.
    * Click **OK** to complete the configuration of the SQL Server VM.
9. Click **Create** to build your VM.  Note that it may take 10-15 minutes to build out your VM 

### Connect to VM 
1. At the top of the Azure portal, enter **SQLVM**. When **SQLVM** appears in the search results, select it. Select the **Connect** button.
2. After selecting the Connect button, click on **Download RDP** file.
3. Enter the user name and password you specified when creating the VM. You may need to select **More choices**, then **Use a different account,** to specify the credentials you entered when you created the VM.
4. Select **OK**.
5. Once the desktop renders, click **No** on the Networks blade.

### Map a drive to an Azure Files Share
Before you can complete this section, you will need to map a drive to an Azure File Share to obtain sample data.
1. Open a command prompt.
2. Key in (cut and paste) the following and hit enter:
    * *net use Z: \\\wagsazurefiles.file.core.windows.net\sampledata /u:AZURE\wagsazurefiles tCfYh37xGNjIc0czqfTW9+kUHIIhlxRUPh9h4YtD/hh7FiFPn1v32RH7uV0a83E6nAa6kkVU6d+nAAeoBItpJg==*
3. Once Z: is mapped change to the Z: drive.
4. Confirm that you can see a file named sampledata.xlsx

### Install Data Connectivity Components
We need to install components to allow Microsoft Office files to be read as data sources.

1. In Server Manger, disable IE Enhanced Security Configuration for both Administrators nad Users.
2. Open Internet Explorer, accepting the defaults.
3. Surf to https://www.microsoft.com/en-us/download/details.aspx?id=39358 and install the Microsoft Acess 64-bit runtime.
4. Installation should take 5 minutes or less.  Click **Close** when installation is complete.

### Connect to SQL
1. Back on the desktop click the **Start Button**,  under the letter "M" choose **Microsoft SQL Server Management Studio** under **Microsoft SQL Server Tools 17**.
3. In the **Connect to Server** or **Connect to Database Engine** dialog box, edit the Server name value. Enter your VM's public IP address. Then add a comma, and add the custom port, 1401, that we specified when you configured the new VM. For example, 111.222.333.444,1401.
4. In the Authentication box, select **SQL Server Authentication**.
5. In the Login box, type the name of a valid SQL login from step 5 in the previous section.
6. In the Password box, type the password of the login from step 5 in the previous section.
7. Click **Connect**.

### Create a new Database and Import Data
1. Under **SQLVM**, right-click Databases then New Database ...
2. Enter **SampleData** as the Database name and click **OK**.
3. Once the database is created, right-click **SampleData**, then **Tasks**, then **Import Flat File**
4. On the Introduction screen click **Next**.
5. On the **Specify Input File** screen select click **Browse**, then select  the Z: Drive, then **sampledata.txt**, then click **Open**.  Click **Next**.
6. Review the information on the Preview Data screen and click **Next**.
7. On the Modify Columns screen set *zip* as the Primary Key, enable **Allow Nulls** on all columns, and then click **Next**.
8. Click **Finish** on the Summary screen.
9. Click **Close** on the Results screen

### Install the Data Migration Assistant
1. From the SQLVM, download and install the [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 or later using the defaults.
2. On the Completed screen, check the box for **Launch Microsoft Data Migration Assistant** and click **Finish**.

### Assess your on-premises database
Before you can migrate data from an on-premises SQL Server instance to a single database or pooled database in Azure SQL Database, you need to assess the SQL Server database for any blocking issues that might prevent migration. 

1. In DMA, select the  (+) icon, and then select  **Assessment** as the project type.
2 Specify **SampleDataAssessment** as the project name.
3.  in the Source server type text box, select **SQL Server**, and in the Target server type text box, select **Azure SQL Database**. Sselect **Create** to create the project.
4.  Select **Next** on the Options screen.
5. On the Select sources screen, enter the following and then select **Connect**:
    * Server name: Enter your VM's public IP address. Then add a comma, and add the custom port, 1401, that we specified when you configured the new VM. For example, 111.222.333.444,1401.
    * Uncheck **Encrypt connection** under **Connection properties**.  Under normal circumstances we would use this option but we did not configure certificates on the source SQL server.
    * Authentication type: SQL Server Authentication
    * In the Username box, type the name of a valid SQL login from the previous steps.
    * In the Password box, type the password of the login from the previous steps.
    *  Click **Connect**.
6. Select **SampleData** and the click **Add**.
7. Select **Begin Assessment**.
8. Review the results and the select **Compatibility issues** radio button in the upper left ahnd corner to see if there are any compatibility issues with your database.


### Provisioned an Azure SQL database
Before you create a migration project in DMA, be sure that you have already provisioned an Azure SQL database.

1. Select **Create a resource** in the upper left-hand corner of the Azure portal.
2. Select **Databases** and then select **SQL Database**.
3. In the Create SQL Database form, type or select the following values and select **Review + Create**:
    * Resource group: Select *Create new*, type **MySQLDBs**. 
    * Database name: Enter **mySampleDatabase**.
    * Server:  Select *Create new*, type *yourinitials*+*todayshortdate*. Example: `abc032019`
    * Server admin login: *pick one* and write it down
    * Password: *Complex.Password*
    * Confirm Password: *Complex.Password*
    * Click **Select**




### Migrate the sample schema
After you're comfortable with the assessment and satisfied that the selected database is a viable candidate for migration to a single database or pooled database in Azure SQL Database, use DMA to migrate the schema to Azure SQL Database.

1. In the Data Migration Assistant, select the New (+) icon, and then under Project type, select Migration.

Specify a project name, in the Source server type text box, select SQL Server, and then in the Target server type text box, select Azure SQL Database.

Under Migration Scope, select Schema only.

After performing the previous steps, the DMA interface should appear as shown in the following graphic:

Create Data Migration Assistant Project

Select Create to create the project.

In DMA, specify the source connection details for your SQL Server, select Connect, and then select the AdventureWorks2012 database.

Data Migration Assistant Source Connection Details

Select Next, under Connect to target server, specify the target connection details for the Azure SQL database, select Connect, and then select the AdventureWorksAzure database you had pre-provisioned in Azure SQL Database.

Data Migration Assistant Target Connection Details

Select Next to advance to the Select objects screen, on which you can specify the schema objects in the AdventureWorks2012 database that need to be deployed to Azure SQL Database.

By default, all objects are selected.

Generate SQL Scripts

Select Generate SQL script to create the SQL scripts, and then review the scripts for any errors.

Schema Script

Select Deploy schema to deploy the schema to Azure SQL Database, and then after the schema is deployed, check the target server for any anomalies.

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
 
