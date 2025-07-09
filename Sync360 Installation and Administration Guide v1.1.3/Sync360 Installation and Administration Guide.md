# INTRODUCTION

This document describes the installation process, structure, configuration, and execution of the Sync360 engine.

# REQUIREMENTS

### Hardware Requirements

#### Minimum hardware requirements:

| <b>Components</b> | <b>Requirements</b>                    |
|-------------------|----------------------------------------|
| Processor         | Dual core processor or 2 virtual cores |
| Memory            | 8 GB RAM or more                       |
| Hard Disk         | 1 GB of available hard disk space      |

Recommended hardware requirements:

| Components       | Requirements                       |
|------------------|------------------------------------|
| Processor        | 4 core processor (2.8 GHz)         |
| Memory           | 16 GB RAM or more                  |
| <b>Hard Disk</b> | 60 GB of available hard disk space |

**NOTE:** Actual hardware requirements may vary based on your specific integration requirements. The number and frequency of integrations can cause the processor and memory requirements to increase.

**NOTE:** In case your company operates large volumes of data, it is recommended to use four processors instead of two

## Software Requirements

The following table lists the software requirements:

| <b>Components</b>          | <b>Requirements</b>                                                                         |
|----------------------------|---------------------------------------------------------------------------------------------|
| <b>Operating System</b><br/> (Recommended versions)    | <b>Windows Server 2022</b></br>$•$ Desktop Experience must be enabled. It is not enabled by default.<br> $•$ .Net framework is loaded automatically, so there's no need to install it separately.<br/> <b>Windows Server 2019</b><br/>  $•$ Desktop Experience must be enabled. It is not enabled by default.                           |
| Operating System</br>(Other versions)            | Windows 10 (64-bit)<br/>Windows 8.1 (64-bit)<br>Windows 7 (64-bit)    |
| .NET                       | Microsoft .NET Framework 4.6.2                                                              |
| Windows Features<          | Windows Identity Foundation (Only for connection to Dynamics CRM<br>On-Premise)             |

## Network Requirements

The only network requirements are to make sure that the users who will be configuring / testing the Sync360 integration scripts and the service account used to start the Sync360 service have the ability to access any network resources / applications required for integration with the Sync360 server. This includes file shares, databases, clouds apps (Dynamics CRM Online), etc.

## Default Installation Paths

| Folder purpose         |                Path           |
|------------------------|-------------------------------|
| Log directory:       | C:\Program Files\Sync360\Logs |
| Root directory:       | C:\Program Files\Sync360      |

# SYNC360 SERVICE CONFIGURATION

By default, the service is configured to run under local service account. Depending on requirements, a service might be required to run under domain service account. It is important to keep that in mind during sync360 upgrades and restore the configuration after each update. Use4si.ProcessingService Properties (Local Computer)

It is recommended to configure service auto restart in case of failure.

## **STRUCTURE**

After the successful installation of Sync360, let's take a look at the program folder structure.

- Sync360 main folder
  - Applications folder for logical applications
    - @System - System system application
    - Sync360App - application bundled with engine
      - Config - Contains configuration for application
      - Instances  - Contains scheduler for application
      - Scripts - Contains application scripts
        - @private  - Special folder for subscripts
        -  Ranges - Contains application processing object
  - Config - Contains default configuration file
- ExProperties -  Contains EWS default exchange properties
- Instances - Default scheduler configuratio
- Logs - Default logs folder
- Scripts - Default scripts folder

## Sync360 Folder

The main **Sync360** folder contains .dll and .exe files. **Use4si.Develop.exe**, the engine for executing the scripts, is of particular importance here. It is described in the 'Using Engine' section.

The main **Sync360** folder contains the following folders (listed in order of importance):

- **Config**
- **Applications**
- **Instances**
- **Scripts**
- **ExProperties**
- **Logs**

## Config Folder

The **Sync360\Config** folder contains the main **default.xml** configuration file. Using this file Sync360 connects to the engine's SQL database.

```
 <connections>
         <add
             name="configdb"
             type="db"
             version="1.0"
             user="sa"
             password="uGBLYTSXXXXXXXXXXXXvboBZHA=="
             url="ProviderType=System.Data.SqlClient;Data 
Source=192.168.1.94;Initial Catalog=use4si;uid={user};pwd={password}"
            org=""/>
     </connections>
```

To allow the engine to connect to correct data sources, you should manually modify the configuration file. You can either modify the **default.xml** file within the **Sync360\Config** folder or the **default.xml** file within the **Sync360\Applications\[name of application 1]\Config** folder, which is described below.

**NOTE:** Passwords are encrypted by adding the **Encrypt Passwords** setting to *default.xml* and setting its value to **"1"**.

### Applications Folder

The **Sync360\Applications** folder, as the name implies, contains applications. In Sync360 there is a logical concept of Applications. Each folder within the **Sync360\Applications** folder is a separate application with its own set of configs, instances and scripts.

By default, the **Sync360\Applications** folder contains two predefined applications:

- **@System**—is used by engine and should not be modified.
- **Sync360LogArchive**—this application responsible for archivin sync360 logs.


#### Instances.xml

The **Sync360\Applications** folder contains the **Instances.xml** file that defines which applications should run under service. There can be more than one application running at the same time.

**Instances.xml** file has the following structure:

```
<?xml version="1.0" encoding="utf-8" ?>
<instances>
    <add>
[instanceconfiguration].[name of application 1]@[datasourceconfiguration]
    \langle add\rangle</instances>
```

Instead of the **[instanceconfiguration]**, **[name of application 1]**, **[datasourceconfiguration]** you should enter the real folder's and files' names.

- **[instanceconfiguration].xml** file is located in the **Sync360\Applications\[name of application 1]\Instances** folder, e.g., *default.xml*.
- **[name of application 1]** is the name of the application folder, e.g., *ContactSync*.
- **[datasourceconfiguration].xml** file is located in the **Sync360\Applications\[name of application 1]\Config** folder, e.g., *default.xml, dev.xml. prod.xml*.

```
<?xml version="1.0" encoding="utf-8" ?>
<instances>
     <add>default.ContactSync@dev</add>
</instances>
```

### Scripts Folder

The **Sync360\Scripts** folder contains scheduler scripts that are used by applications to schedule their operations.

#### ScriptScheduler.xml

The scheduler script. The scheduler method schedules scripts individually. Each script affected by the ScriptScheduler has its own schedule and is executed independently from other scripts.

Image /page/15/Figure/2 description: This image displays a configuration for scheduling tasks using the ScriptScheduler. It shows an XML structure with two main components: 'schedule' and 'config'. Within the 'schedule', there is a single task entry titled 'SyncExistingContacts' which has attributes including 'task' set to 'ScriptScheduler', 'period' to 600, and includes nested parameters named 'Criteria', 'ScriptName', and 'Interval'. The 'Criteria' parameter value is 'SyncEmployees', while the 'ScriptName' and 'Interval' parameters have values of 'SyncExistingContacts' and 1200 respectively.

#### ScriptBatchScheduler.xml

The batch scheduler script. The batch scheduler method groups up several application scripts into a single batch, so that the scripts would execute one after another for each object.

To use the batch scheduler method, the scripts within **default.xml** (*Sync360\Applications\[name of application 1]\Instances*) must have an additional queue parameter.

```
<?xml version="1.0" encoding="utf-8" ?>
<config>
  <schedule>
   <add                                   
task="ScriptBatchScheduler" period="900">
     <param name="Criteria">SyncEmployees</param>
     <param name="ScriptName">CreateNewContactsInCrm</param>
     <param name="Interval">300</param>
   </add>
    <add                                   
task="ScriptBatchScheduler" period="900">
     <param name="Criteria">SyncEmployees</param>
     <param name="ScriptName">CreateNewContactsInExchange</param>
     <param name="Interval">300</param>
   </add>
```

## Application Structure

Every application consists of three parts, which are represented as folders—**Config**, **Instances** and **Scripts**:

Image /page/15/Picture/9 description: The image depicts a serene landscape with rolling hills covered in lush green vegetation. In the foreground, a clear blue river flows gently between banks of vibrant wildflowers. A small wooden bridge spans across the river, providing access to the other side where more hills and trees await. The sky above is a perfect canvas for soft, fluffy clouds that drift lazily across the sunlit expanse.

Here's another presentation of the folder structure.

Image /page/16/Figure/1 description: A group of five individuals are seated around a table discussing various topics including financial planning, career development, and health habits. The participants appear to be in their mid-thirties to early forties, dressed professionally in business attire. One person is taking notes on a notepad while another has a laptop open displaying graphs related to personal finance.

### Config Folder

The **Sync360\Applications\[name of application 1]\Config** folder contains **default.xml** with connections to data sources.

The real connection looks similar to the example below. Specify correct values for the user, password, domain, and URL to connect. For CRM also specify the organization name (org).

```
<?xml version="1.0" encoding="utf-8" ?>
<config>
 <connections>
   <add
 name="crm"
 type="crm"
 version="2011"
 user="XXXXXXXXXXXXX"
 password="XXXXXXXXX"
 domain="XXXXXXXX"
 url="http://XXXXXXXXXX"
 org="XXXXXXXXXX"/>
    <add name="exchange" type="exchange" version="2016" user="XXXXXXXXXXX"
password="XXXXXXX" domain="XXXX" url="https://XXXXXXXXXXXXX" />
  </connections>
</config>
```

**NOTE:** Almost all connection examples are described in the '*Sync360 Connectors Description*' document.

### Instances Folder

The **Sync360\Applications\[name of application 1]\Instances** folder contains **default.xml** file. That **default.xml** controls every aspect of scheduling for that application:

- name—a name of the scheduling job.
- task—an actual name of the scheduling script used for that application script (see the *Sync360\Scripts* folder description above).
- period—after each period the scheduler checks whether there is still an object to be processed, if not, the application script gets unscheduled. Measured in seconds.
- Criteria—a name of the script that defines objects for processing (see the *Ranges* folder description).
- ScriptName—an actual name of the application script located in *Sync360\Applications\[name of application 1]\Scripts*, e.g., CreateNewContactsInCRM, SyncExistingCintacts, etc.
- Interval—an interval that defines how often the script should be executed in seconds.

#### For example, this is a part of the **default.xml** file:

```
<config>
     <schedule>
         <add name="SyncExistingContacts" task="ScriptScheduler" period="600">
             <param name="Criteria">SyncEmployees</param>
             <param name="ScriptName">SyncExistingContacts</param>
             <param name="Interval">1200</param>
         </add>
      </schedule>
</config>
```

### Scripts Folder

The **Sync360\Applications\[name of application 1]\Scripts** folder contains all application scripts, which perform data processing, and two subfolders **@Private** and **Ranges.**

- **@private**
- **Ranges**
- CleanCrmContacts
- CleanExchangeContacts
- CreateNewContactsInCrm
- PreCreateNewContactsInCrm
- ShakeFrozenExchangeContacts
- SyncExistingContacts
  
  - **@Private** this is a special folder where the engine tries to find scripts which are invoked by the Call operator.
  - **Ranges** this folder is used by scheduler to locate scripts that define objects for processing.

#### Ranges Folder

The folder contains the script that defines ObjectsForProcessing. The script is referenced by the scheduler script through the Criteria parameter and  $\circ$ bjectIds are passed down to application scripts for record processing.


For example, the script may be called SyncEmployees.xml and contain an object for employee records:
```
<script>
    <select from="crm" entity="vs360_employee" into="employees">
        <query>
        <![CDATA[
            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="true">
              <entity name="vs360_employee">
                <attribute name="vs360_employeeid" />
                <filter type="and">
                  <condition attribute="statecode" operator="eq" value="0" />
                </filter>
                <link-entity name="vs360_vs360_employee_team" from="vs360_employeeid" to="vs360_employeeid" visible="false" intersect="true">
                  <link-entity name="team" from="teamid" to="teamid" alias="aa">
                    <filter type="and">
                       <condition attribute="name" operator="eq" value="SyncEmployees" />
                    </filter>
                  </link-entity>
                </link-entity>
              </entity>
            </fetch>
        ]] >
        </query>
    </select>
    <var ObjectsForProcessing="new List()"/>
    <for var="employee" in="employees">
        <set var="ObjectsForProcessing[]">{employee.vs360_employeeid.ToString()}</set>
   </for>
</script>
```

• the Criteria parameter in *Sync360\Applications\[name of application 1]\Instances\***default.xml** refers to that file.

```
<config>
     <schedule>
         <add name="SyncExistingContacts" task="ScriptScheduler" period="60">
             <param name="Criteria">SyncEmployees</param>
             <param name="ScriptName">SyncExistingContacts</param>
             <param name="Interval">120</param>
         </add>
     </schedule>
</config>
```

Application scripts expect object id from that file.

```
<script>
 <log></log>
 <log>Create new contacts in Crm from Exchange.</log>
 <log></log>
 <sandbox>
   <var Employee='["EmployeeId" : (Input.ObjectId ?? "E4340742-XXXX-0C295CA5D3")]'/>
   <var SyncState="new Object()"/>
```

# EXECUTION

During the script execution the engine does the following:

- 1. Tries to find the **[instanceconfiguration].xml** file in the **Sync360\Applications\[name of application 1]\Instances** folder. If it has not been found there, it will check for this file in the **Sync360\Instances** folder, and if nothing has been found there too, the scheduler configuration will be skipped.
- 2. Reads the **Sync360\Config\default.xml** file, then reads the **Sync360\Applications\[name of application 1]\Config\default.xml** file and finally reads the **Sync360\Applications\[name of application 1]\Config\[datasourceconfiguration].xml** file. All configuration files are combined into one, in predefined order.

Based on this example, let's take a look at what the engine will do at the runtime:

- 1. Try to find the **default.xml** file in the **Sync360\Applications\ContactSync\Instances** folder. If it's not found it will check for this file in the **Sync360\Instances** folder, and if it's not found there too, the scheduler configuration will be skipped.
- 2. Read the **Sync360\Config\default.xml** file, then read the **Sync360\Applications\ContactSync\Config\default.xml** file and finally read the **Sync360\Applications\ContactSync\Config\dev.xml** file. All configuration files will be combined into one, in predefined order.
