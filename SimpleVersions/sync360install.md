|  |  |
| --- | --- |
| ![](data:image/png;base64...)  Prepared By:  AN |  |

Sync360 Installation and Administration Guide

v.1.1.3 (241022)

This document and its supporting materials are proprietary and confidential.

The information may not be disclosed to a third party without the express permission of HSO.

Contributors

| **Name** | **Role** | **Company** |
| --- | --- | --- |
| Roman Rudenko |  |  |
| Alexandra Petrova |  |  |
| Alexander Novikov |  |  |

Document Revision History

NOTE: This section contains only content changes made to the document. Solution changes have been moved to the [Release Notes](#Release) section and are now located at the bottom of the document.

| **Version** | **Section** | **Content Change** | **Date** |
| --- | --- | --- | --- |
| 1.1.3 (241022) | UPDATE | | 06/23/25 |
| Sync360 Service Configuration | * Renamed Sync360 Service Recovery Configuration to Sync360 Service Configuration * Added description for service account used to run the sync360 service. * Moved Sync360 Service Recovery Configuration to subsection. |
| 1.1.2 (241022) | UPDATE | | 01/28/25 |
| Release Notes | * Updated version 241022 |
| 1.1.1 (241022) | UPDATE | | 10/24/2024 |
| Release Notes | * Added versions 241022, 240614 |
| 1.1 (231130) | NEW | | 07/10/2024 |
| Appendix | * Created the Appendix section. * Created the Release Notes section within Appendix. |
| Document Information | * Created the Document Information section. |
| Systems Interface Details | * Created the Systems Interface Details section. * Added the System interface diagram. |
| Sync360 Service Recovery Configuration | * Created the Sync360 Service Recovery Configuration section. * Added the Service recovery configuration settings. |
| UPDATE | |
| Document Revision History | * Changed the name of the section from ‘Revision History’ to ‘Document Revision History’. * Added a note. * Changed the structure of the section. |
| Release Notes | * Moved description of solution changes to the Release Notes section. |
| Requirements | Added the latest OS requirements. |
| 1.0 | NEW | | 09/13/2023 |
| All sections | * Document structure rewritten; structure schemes added; descriptions are now based on the current default version of Sync360. |
| Application Structure | * Described scheduling methods and object processing feature. |
| 0.5 | UPDATE | | 08/05/2022 |
| Installation | * Updated the section. |
| Using Engine | * Updated the section. |
| 0.4 | UPDATE | | 09/17/2021 |
| Requirements | * Updated Software and hardware requirements. |
| 0.3 | **NEW** | | 08/26/2021 |
|  | * Added the Encryption note. |
| 0.2 | NEW | | 05/23/2017 |
| All sections | * Second Version of the document was prepared. |
| 0.1 | NEW | | 03/28/2014 |
| All sections | * First Version of the document was prepared. |

Document Information

**Audience**

This document is intended for HSO developers. This may be forwarded to responsible employees on the client’s side as appropriate.

**Purpose**

Provide guidance on installation and administration of Sync360 Engine, its configuration, structure, and interface.

Table of Contents

[Introduction 6](#_Toc201583994)

[Requirements 7](#_Toc201583995)

[Hardware Requirements 7](#_Toc201583996)

[Software Requirements 7](#_Toc201583997)

[Network Requirements 8](#_Toc201583998)

[Systems Interface Details 8](#_Toc201583999)

[Installation 9](#_Toc201584000)

[Installation via Graphic Interface 9](#_Toc201584001)

[Installation via Command Line 11](#_Toc201584002)

[Default Installation 11](#_Toc201584003)

[Installation with Arguments 12](#_Toc201584004)

[Sync360 Service Configuration 13](#_Toc201584005)

[Sync360 Service Recovery Configuration 13](#_Toc201584006)

[Update 14](#_Toc201584007)

[Structure 18](#_Toc201584008)

[Sync360 Folder 19](#_Toc201584009)

[Config Folder 19](#_Toc201584010)

[Applications Folder 20](#_Toc201584011)

[Scripts Folder 20](#_Toc201584012)

[Application Structure 21](#_Toc201584013)

[Config Folder 22](#_Toc201584014)

[Instances Folder 23](#_Toc201584015)

[Scripts Folder 23](#_Toc201584016)

[Execution 26](#_Toc201584017)

[Using Engine 27](#_Toc201584018)

[Appendix 31](#_Toc201584019)

[Release Notes 31](#_Toc201584020)

Introduction

This document describes the installation process, structure, configuration, and execution of the Sync360 engine.

Requirements

Hardware Requirements

Minimum hardware requirements:

| **Components** | **Requirements** |
| --- | --- |
| Processor | Dual core processor or 2 virtual cores |
| Memory | 8 GB RAM or more |
| Hard Disk | 1 GB of available hard disk space |

Recommended hardware requirements:

| **Components** | **Requirements** |
| --- | --- |
| Processor | 4 core processor (2.8 GHz) |
| Memory | 16 GB RAM or more |
| Hard Disk | 60 GB of available hard disk space |

NOTE: Actual hardware requirements may vary based on your specific integration requirements. The number and frequency of integrations can cause the processor and memory requirements to increase.

NOTE: In case your company operates large volumes of data, it is recommended to use four processors instead of two

Software Requirements

The following table lists the software requirements:

| **Components** | **Requirements** |
| --- | --- |
| Operating System  (**Recommended versions**) | **Windows Server 2022**   * Desktop Experience **must** be enabled. It is **not** enabled by default. * .Net framework is loaded automatically, so there’s no need to install it separately.   **Windows Server 2019**   * Desktop Experience **must** be enabled. It is **not** enabled by default.   Windows Server 2012 / 2012 R2 / 2016 |
| Operating System  (Other versions) | Windows 10 (64-bit)  Windows 8.1 (64-bit)  Windows 7 (64-bit) |
| .NET | Microsoft .NET Framework 4.6.2 |
| Windows Features | Windows Identity Foundation (Only for connection to Dynamics CRM On-Premise) |

Network Requirements

The only network requirements are to make sure that the users who will be configuring / testing the Sync360 integration scripts and the service account used to start the Sync360 service have the ability to access any network resources / applications required for integration with the Sync360 server. This includes file shares, databases, clouds apps (Dynamics CRM Online), etc.

Systems Interface Details

![](data:image/png;base64...)![](data:image/png;base64...)

Installation

Installation via Graphic Interface

Sync360 engine is shipped with one file of **.msi** extension. The installation is straightforward.

1. Navigate to your Sync360 .msi file and execute it. Example, *Sync360\_220804.msi*
2. On the welcome screen click **Next**.

![](data:image/png;base64...)

1. Specify installation folder or use the default one. We recommend installing to the default folder. Click **Next**.

![Graphical user interface, text, application, email  Description automatically generated](data:image/png;base64...)

1. On the next screen click **Next** to begin installation.

![](data:image/png;base64...)

1. During installation, the dialog will be displayed asking you to specify location for Log files and the Applications files root directory. Use the default or change it to any other. Besides, we recommend leaving the NTFS compression setting enabled. Click **OK** to continue.

![Graphical user interface, text, application, email  Description automatically generated](data:image/png;base64...)

NOTE: Regardless of where you specify the Root and Log directories, you will need to grant the users who need to maintain and troubleshoot the Sync360 scripts full access to these folders (read, write, create, delete).

This is especially true if you choose the default folders. This is due to local admin rights are required to modify files in the Program Files folder. It will be the best practice to create a group for Sync360 administrators, add users to this group, and grant the group the necessary access to these folders.

1. After installation has finished, click **Close** to exit the wizard.

![](data:image/png;base64...)

Installation via Command Line

Default Installation

Installation via Command Line is faster and more convenient in some cases. It can also be performed in a silent mode.

1. Launch your Command Line application, e. g. *Windows PowerShell*.
2. To perform default and silent installation simply enter the following command and execute it:

Start-Process msiexec.exe -Wait -ArgumentList '/I {path to installer} /q'

NOTE: Where {path to installer} is a path to the Sync360 .msi file.

![](data:image/png;base64...)

1. After that, Sync360 will be installed into the default directory and no notifications will appear after installation will be completed.

Installation with Arguments

If you need to specify application or log directories or disable NTFS log compression, use the argument examples listed below, and change their values according to your preferences.

* Application directory location:

ApplicationsFolder="C:\Program Files\Sync360"

* Log directory location:

LogsFolder="C:\Program Files\Sync360\Logs"

* NTFS compression for Log directory:

LogsFolderCompression="True"

If all arguments have been added, the command will look similar to the example below.

![](data:image/png;base64...)

Sync360 Service Configuration

By default, the service is configured to run under local service account. Depending on requirements, a service might be required to run under domain service account. It is important to keep that in mind during sync360 upgrades and restore the configuration after each update.

![](data:image/png;base64...)

Sync360 Service Recovery Configuration

It is recommended to configure service auto restart in case of failure.

![A screenshot of a computer error  Description automatically generated](data:image/png;base64...)

Update

CAUTION: To update Sync360 1.24.0410 or newer, follow the standard installation procedure. For more info on installation, please refer to ‘Installation via Graphic Interface’. We recommend creating backups before every update.

Unlike clean installation, update to version 1.24.0410 or newer from the old version of Sync360 requires manual actions. To update, do the following:

1. In your Windows Server system, navigate to the folder where Sync360 is currently installed, e.g. …\Program Files\Sync360
2. Backup the contents of the Sync360 folder.

![](data:image/png;base64...)

?

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

NOTE: ‘Logs’ folder usually takes up a lot of space, so you would want to evaluate whether you need to back it up or not.

1. Navigate to ‘Settings’ ? ‘Apps & Features’ and uninstall Sync360.

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

1. Navigate back to the Sync360 folder and manually delete the following files from that folder:
   * Use4si.Develop.exe
   * Use4si.ProcessingService.exe
   * Use4si.WorkerProcess.exe
   * Use4si.Develop.exe.config
   * Use4si.ProcessingService.exe.config
   * Use4si.WorkerProcess.exe.config
   * Use4si.ProcessingService.InstallState

![](data:image/png;base64...)

NOTE: Any custom files left from the previous installation can be preserved if you still need them.

1. Locate the installer file, e.g., Sync360\_240410.msi, and launch the standard installation procedure. The new version is installed to the same Sync360 folder. For more info on installation, please refer to ‘Installation via Graphic Interface’.

![](data:image/png;base64...)

1. After installation is complete, navigate to backup and copy ‘Instances.xml’ from the ‘Applications’ folder.

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

1. Navigate to fresh installation and paste ‘Instances.xml’ to the ‘Applications’ folder.

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

1. Open ‘Apps & Features’ and check the version of an updated Sync360.

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

Structure

After the successful installation of Sync360, let’s take a look at the program folder structure.

![](data:image/png;base64...)

Here’s another presentation of the folder structure.

![A diagram of a company structure  Description automatically generated](data:image/png;base64...)

Sync360 Folder

The main **Sync360** folder contains .dll and .exe files. **Use4si.Develop.exe**, the engine for executing the scripts, is of particular importance here. It is described in the ‘Using Engine’ section.

The main **Sync360** folder contains the following folders (listed in order of importance):

* **Config**
* **Applications**
* **Instances**
* **Scripts**
* **ExProperties**
* **Logs**

Config Folder

The **Sync360\Config** folder contains the main **default.xml** configuration file. Using this file Sync360 connects to the engine’s SQL database.

<connections>

<add

name="configdb"

type="db"

version="1.0"

user="sa"

password="uGBLYTSXXXXXXXXXXXXvboBZHA=="

url="ProviderType=System.Data.SqlClient;Data Source=192.168.1.94;Initial Catalog=use4si;uid={user};pwd={password}"

org=""/>

</connections>

To allow the engine to connect to correct data sources, you should manually modify the configuration file. You can either modify the **default.xml** file within the **Sync360\Config** folderor the **default.xml** file withinthe **Sync360\Applications\[name of application 1]\Config** folder,which is described below.

NOTE: Passwords are encrypted by adding the **Encrypt Passwords** setting to *default.xml* and setting its value to **“1”**.

![Èçîáðàæåíèå âûãëÿäèò êàê òåêñò  Àâòîìàòè÷åñêè ñîçäàííîå îïèñàíèå](data:image/png;base64...)

Applications Folder

The **Sync360\Applications** folder, as the name implies, contains applications. In Sync360 there is a logical concept of Applications. Each folder within the **Sync360\Applications** folder is a separate application with its own set of configs, instances and scripts.

By default, the **Sync360\Applications** folder contains two predefined applications:

* **@System**—is used by engine and should not be modified.
* **Sync360LogArchive**—this application gathers and stores synchronization logs.

![](data:image/png;base64...)

Instances.xml

The **Sync360\Applications** folder contains the **Instances.xml** file that defines which applications should run under service. There can be more than one application running at the same time.

**Instances.xml** file has the following structure:

<?xml version="1.0" encoding="utf-8" ?>

<instances>

<add>

[instanceconfiguration]**.**[name of application 1]@[datasourceconfiguration]

</add>

</instances>

Instead of the **[instanceconfiguration]**, **[name of application 1]**, **[datasourceconfiguration]** you should enter the real folder’s and files’ names.

* **[instanceconfiguration].xml** file is located in the **Sync360\Applications\[name of application 1]\Instances** folder, e.g., *default.xml*.
* **[name of application 1]** is the name of the application folder, e.g., *ContactSync*.
* **[datasourceconfiguration].xml** file is located in the **Sync360\Applications\[name of application 1]\Config** folder, e.g., *default.xml, dev.xml. prod.xml*.

<?xml version="1.0" encoding="utf-8" ?>

<instances>

<add>default.ContactSync@dev</add>

</instances>

Scripts Folder

The **Sync360\Scripts** folder contains scheduler scripts that are used by applications to schedule their operations.

ScriptScheduler.xml

The scheduler script. The scheduler method schedules scripts individually. Each script affected by the ScriptScheduler has its own schedule and is executed independently from other scripts.

<config>

<schedule>

<add name="SyncExistingContacts" task="ScriptScheduler" period="600">

<param name="Criteria">SyncEmployees</param>

<param name="ScriptName">SyncExistingContacts</param>

<param name="Interval">1200</param>

</add>

</schedule>

</config>

ScriptBatchScheduler.xml

The batch scheduler script. The batch scheduler method groups up several application scripts into a single batch, so that the scripts would execute one after another for each object.

To use the batch scheduler method, the scripts within **default.xml** (*Sync360\Applications\[name of application 1]\Instances*) must have an additional queue parameter.

<?xml version="1.0" encoding="utf-8" ?>

<config>

  <schedule>

    <add queue="BatchQueue" name="CreateNewContactsInCrm" task="ScriptBatchScheduler" period="900">

      <param name="Criteria">SyncEmployees</param>

      <param name="ScriptName">CreateNewContactsInCrm</param>

      <param name="Interval">300</param>

    </add>

    <add queue="BatchQueue" name="CreateNewContactsInExchange" task="ScriptBatchScheduler" period="900">

      <param name="Criteria">SyncEmployees</param>

      <param name="ScriptName">CreateNewContactsInExchange</param>

      <param name="Interval">300</param>

    </add>

Application Structure

Every application consists of three parts, which are represented as folders—**Config**, **Instances** and **Scripts**:

![A black and white text  Description automatically generated](data:image/png;base64...)

Here’s another presentation of the folder structure.

![](data:image/png;base64...)

Config Folder

The **Sync360\Applications\[name of application 1]\Config** folder contains **default.xml** with connections to data sources.

The real connection looks similar to the example below. Specify correct values for the user, password, domain, and URL to connect. For CRM also specify the organization name (org).

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

    <add name="exchange" type="exchange" version="2016" user="XXXXXXXXXXX" password="XXXXXXX" domain="XXXX" url="https://XXXXXXXXXXXXX" />

  </connections>

</config>

NOTE: Almost all connection examples are described in the ‘*Sync360 Connectors Description*’ document.

Instances Folder

The **Sync360\Applications\[name of application 1]\Instances** folder contains **default.xml** file. That **default.xml** controls every aspect of scheduling for that application:

* name—a name of the scheduling job.
* task—an actual name of the scheduling script used for that application script (see the *Sync360\Scripts* folder description above).
* period—after each period the scheduler checks whether there is still an object to be processed, if not, the application script gets unscheduled. Measured in seconds.
* Criteria—a name of the script that defines objects for processing (see the *Ranges* folder description).
* ScriptName—an actual name of the application script located in *Sync360\Applications\[name of application 1]\Scripts*, e.g., CreateNewContactsInCRM, SyncExistingCintacts, etc.
* Interval—an interval that defines how often the script should be executed in seconds.

For example, this is a part of the **default.xml** file:

<config>

<schedule>

<add name="SyncExistingContacts" task="ScriptScheduler" period="600">

<param name="Criteria">SyncEmployees</param>

<param name="ScriptName">SyncExistingContacts</param>

<param name="Interval">1200</param>

</add>

</schedule>

</config>

Scripts Folder

The **Sync360\Applications\[name of application 1]\Scripts** folder contains all application scripts, which perform data processing, and two subfolders **@Private** and **Ranges.**

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

* **@Private** — this is a special folder where the engine tries to find scripts which are invoked by the Call operator.
* **Ranges** — this folder is used by scheduler to locate scripts that define objects for processing.

Ranges Folder

The folder contains the script that defines ObjectsForProcessing. The script is referenced by the scheduler script through the Criteria parameter and ObjectIds are passed down to application scripts for record processing.

For example, the script may be called **SyncEmployees.xml** and contain an object for employee records:

<script>

    <select from="crm" entity="vs360\_employee" into="employees">

        <query>

        <![CDATA[

            <fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="true">

              <entity name="vs360\_employee">

                <attribute name="vs360\_employeeid" />

                <filter type="and">

                  <condition attribute="statecode" operator="eq" value="0" />

                </filter>

                <link-entity name="vs360\_vs360\_employee\_team" from="vs360\_employeeid" to="vs360\_employeeid" visible="false" intersect="true">

                  <link-entity name="team" from="teamid" to="teamid" alias="aa">

                    <filter type="and">

                      <condition attribute="name" operator="eq" value="SyncEmployees" />

                    </filter>

                  </link-entity>

                </link-entity>

              </entity>

            </fetch>

        ]]>

        </query>

    </select>

    <var ObjectsForProcessing="new List()"/>

    <for var="employee" in="employees">

        <set var="ObjectsForProcessing[]">{employee.vs360\_employeeid.ToString()}</set>

    </for>

</script>

* the Criteria parameter in *Sync360\Applications\[name of application 1]\Instances\***default.xml** refers to that file.

<config>

<schedule>

<add name="SyncExistingContacts" task="ScriptScheduler" period="60">

<param name="Criteria">SyncEmployees</param>

<param name="ScriptName">SyncExistingContacts</param>

<param name="Interval">120</param>

</add>

</schedule>

</config>

* Application scripts expect object id from that file.

<script>

  <log></log>

  <log>Create new contacts in Crm from Exchange.</log>

  <log></log>

  <sandbox>

    <var Employee='["EmployeeId" : (Input.ObjectId ?? "E4340742-XXXX-0C295CA5D3")]'/>

    <var SyncState="new Object()"/>

Execution

During the script execution the engine does the following:

1. Tries to find the **[instanceconfiguration].xml** file in the **Sync360\Applications\[name of application 1]\Instances** folder. If it has not been found there, it will check for this file in the **Sync360\Instances** folder, and if nothing has been found there too, the scheduler configuration will be skipped.
2. Reads the **Sync360\Config\default.xml** file, then reads the **Sync360\Applications\[name of application 1]\Config\default.xml** file and finally reads the **Sync360\Applications\[name of application 1]\Config\[datasourceconfiguration].xml** file. All configuration files are combined into one, in predefined order.

Based on this example, let's take a look at what the engine will do at the runtime:

1. Try to find the **default.xml** file in the **Sync360\Applications\ContactSync\Instances** folder. If it’s not found it will check for this file in the **Sync360\Instances** folder, and if it’s not found there too, the scheduler configuration will be skipped.
2. Read the **Sync360\Config\default.xml** file, then read the **Sync360\Applications\ContactSync\Config\default.xml** file and finally read the **Sync360\Applications\ContactSync\Config\dev.xml** file. All configuration files will be combined into one, in predefined order.

Using Engine

You can also create your own application.

1. Copy the **Sync360\Applications\Sync360LogArchive** folder and paste it to the same folder.
2. Give it a name, for example, **MyApp**:

![A screenshot of a computer  Description automatically generated](data:image/png;base64...)

1. Return to the main **Sync360** folder.The next step will be editing the files and running the scripts using the **Use4si.Develop.exe:**

![A screenshot of a computer program  Description automatically generated](data:image/png;base64...)

**Use4si.Develop.exe** is an .exe file that allows user to execute scripts through the developer interface.

![Graphical user interface, text, application  Description automatically generated](data:image/png;base64...)

During installation a windows service is created, it’s used for processing Applications as described in Configuration section. You can change the start state to automatic after all required preparation is done.

![](data:image/png;base64...)

You can also find an .exe file **Use4si.Develop.exe** in the main Sync360 folder and run it as administrator.

![](data:image/png;base64...)

An **example** of script execution:

![](data:image/png;base64...)

Let’s take a look on **the main features**:

|  |  |
| --- | --- |
| ![](data:image/png;base64...) | Open script file (for example, SyncExistingContacts.xml) |
| ![](data:image/png;base64...) | You can edit multiple scripts |
| ![](data:image/png;base64...) | Put a string in the format “instanceconfiguration**.**nameofapplication@datasourceconfiguration” (for example, **sample@main**) into the textbox |
| ![](data:image/png;base64...) | Press to run the script |
| ![](data:image/png;base64...) | Press to pause/stop the script |
| ![](data:image/png;base64...) | Clear cache from last execution |
| ![](data:image/png;base64...) | Navigate and select application using the engine’s interface. |
| ![](data:image/png;base64...) |  |
| ![](data:image/png;base64...) | Here you can define variables to watch during the script execution |
| ![](data:image/png;base64...) | Output window shows script execution results |

Appendix

Release Notes

| **Version** | **Changes** |
| --- | --- |
| 241022 | * Engine changes: * Removed weak TLS protocols to improve connection’s security. * Connectors cache for Transaction/Batch data sources. * Added the new script scheduler for instances running in multithread mode. * Signature scanner: * Added default length for fields. * Updated dictionaries. * Made multiple improvements to regional patterns. |
| 240614 | * Bynder connector: * Supported extended images. * OData connector: * Improved query translation. * Signature scanner: * Multiple improvements. * VantagePoint connector: * Added automatic token update. * Exchange connector: * Supported Extended Protection onprem deployment. * Engine changes: * Added connectors cache feature. |
| 231130 | * Engine changes: * Removed log4net library. This tool for logging is duplicated by NLog. * Updated NLog. NLog is a logging tool. * Updated Newtonsoft.Json. Newtonsoft.Json is a JSON framework for .NET. * Email parser changes: * Set a length limit for parsing results. Prevents random sets of symbols from being treated as field values. * Develop changes: * Added the monitoring feature to the Sync360 editor. Now, the editor monitors whether the script file has been changed in the file system and suggests reloading it. * CRM connector: * Added the resilience strategy for Crm2011 connector. The resilience strategy repeats the connection attempt if a server hasn’t responded in time. It makes ten connection attempts at certain intervals. * SharePoint connector: * Added the Client Secret auth. Added the new authentication method for SharePoint. |
