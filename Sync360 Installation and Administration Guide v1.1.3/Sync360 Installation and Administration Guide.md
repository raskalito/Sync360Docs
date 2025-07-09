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

| Components       | Requirements                       |  |
|------------------|------------------------------------|--|
| Processor        |                                    |  |
| Memory           |                                    |  |
| <b>Hard Disk</b> | 60 GB of available hard disk space |  |

**NOTE:** Actual hardware requirements may vary based on your specific integration requirements. The number and frequency of integrations can cause the processor and memory requirements to increase.

**NOTE:** In case your company operates large volumes of data, it is recommended to use four processors instead of two

## Software Requirements

The following table lists the software requirements:

| <b>Components</b>          | <b>Requirements</b>                                                                         |
|----------------------------|---------------------------------------------------------------------------------------------|
| <b>Operating System</b>    | <b>Windows Server 2022</b>                                                                  |
| (Recommended versions)     | Desktop Experience must be enabled. It is not enabled by default.<br>$•$                    |
|                            | .Net framework is loaded automatically, so there's no need to install<br>$•$ it separately. |
| <b>Windows Server 2019</b> | Desktop Experience must be enabled. It is not enabled by default.                           |
| Operating System           | Windows 10 (64-bit)                                                                         |
| (Other versions)           | Windows 8.1 (64-bit)<br>Windows 7 (64-bit)                                                  |
| .NET                       | Microsoft .NET Framework 4.6.2                                                              |
| <b>Windows Features</b>    | Windows Identity Foundation (Only for connection to Dynamics CRM<br>On-Premise)             |

## Network Requirements

The only network requirements are to make sure that the users who will be configuring / testing the Sync360 integration scripts and the service account used to start the Sync360 service have the ability to access any network resources / applications required for integration with the Sync360 server. This includes file shares, databases, clouds apps (Dynamics CRM Online), etc.

### Systems Interface Details

Image /page/2/Figure/3 description: A pie chart is displayed with labels indicating the percentage distribution of a group's preferences for different types of entertainment. The categories include 'Movies (35%)', 'Music (25%)', and 'Books (40%)'. Each segment of the pie chart corresponds to these percentages, visually represented by sections that occupy 35%, 25%, and 40% of the circle's area respectively.

## INSTALLATION

### Installation via Graphic Interface

Sync360 engine is shipped with one file of **.msi** extension. The installation is straightforward.

- 1. Navigate to your Sync360 .msi file and execute it. Example, *Sync360\_220804.msi*
- 2. On the welcome screen click **Next**. de Sync360  $\Box$  $\times$ Welcome to the Sync360 Setup Wizard The installer will quide you through the steps required to install Sync360 on your computer. WARNING: This computer program is protected by copyright law and international treaties. Unauthorized duplication or distribution of this program, or any portion of it, may result in severe civil or criminal penalties, and will be prosecuted to the maximum extent possible under the law.  $<sub>8</sub>$  Back</sub>  $Next$ Cancel
- 3. Specify installation folder or use the default one. We recommend installing to the default folder. Click **Next**.

Image /page/3/Picture/6 description: The image is a line graph depicting the average monthly temperatures over four seasons in a city. The x-axis represents the four distinct seasons (Winter, Spring, Summer, Fall), while the y-axis measures temperature in degrees Celsius. Each season has a corresponding line on the graph, smoothly connecting data points that represent daily average temperatures for each season.

4. On the next screen click **Next** to begin installation.

Image /page/4/Picture/0 description: A graph depicting the average monthly temperatures across different cities in a year. The x-axis represents the cities, while the y-axis shows the temperature in degrees Celsius. Notably, Moscow is displayed with an unusually high temperature compared to other cities.

5. During installation, the dialog will be displayed asking you to specify location for Log files and the Applications files root directory. Use the default or change it to any other. Besides, we recommend leaving the NTFS compression setting enabled. Click **OK** to continue.

| Use4si Processing Service installation: Set log directory.          |                               |
|---------------------------------------------------------------------|-------------------------------|
| Log directory:                                                      | C:\Program Files\Sync360\Logs |
| Apply NTFS compression for Log directory, subdirectories and files. |                               |
| Root directory:                                                     | C:\Program Files\Sync360      |

**NOTE:** Regardless of where you specify the Root and Log directories, you will need to grant the users who need to maintain and troubleshoot the Sync360 scripts full access to these folders (read, write, create, delete).

This is especially true if you choose the default folders. This is due to local admin rights are required to modify files in the Program Files folder. It will be the best practice to create a group for Sync360 administrators, add users to this group, and grant the group the necessary access to these folders.

6. After installation has finished, click **Close** to exit the wizard.

| Sync360                                  |  | × |
|------------------------------------------|--|---|
| <b>Installation Complete</b>             |  |   |
| Sync360 has been successfully installed. |  |   |
| Click 'Close' to exit.                   |  |   |

## Installation via Command Line

### Default Installation

Installation via Command Line is faster and more convenient in some cases. It can also be performed in a silent mode.

1. Launch your Command Line application, e. g. *Windows PowerShell*.

| 2. To perform default and silent installation simply enter the following command and execute it: |     |  |  |  |  |
|--------------------------------------------------------------------------------------------------|-----|--|--|--|--|
| Start-Process msiexec.exe -Wait -ArgumentList '/I {path to installer}                            | /q' |  |  |  |  |
| <b>NOTE:</b> Where {path to installer} is a path to the Sync360 .msi file.                       |     |  |  |  |  |

| 2 Windows PowerShell                                                                                 |  |  |
|------------------------------------------------------------------------------------------------------|--|--|
| PS C:\Users\ > Start-Process msiexec.exe -Wait -ArgumentList '/I C:\Downloads\Svnc360 220804.msi /q' |  |  |

3. After that, Sync360 will be installed into the default directory and no notifications will appear after installation will be completed.

#### Installation with Arguments

If you need to specify application or log directories or disable NTFS log compression, use the argument examples listed below, and change their values according to your preferences.

- Application directory location: ApplicationsFolder="C:\Program Files\Sync360"
- Log directory location: LogsFolder="C:\Program Files\Sync360\Logs"
- NTFS compression for Log directory: LogsFolderCompression="True"

If all arguments have been added, the command will look similar to the example below.X Windows PowerShell

> Start-Process msiexec.exe -Wait -ArgumentList 'S C:\Users\

×

۸

 $\Box$ 

# SYNC360 SERVICE CONFIGURATION

By default, the service is configured to run under local service account. Depending on requirements, a service might be required to run under domain service account. It is important to keep that in mind during sync360 upgrades and restore the configuration after each update. Use4si.ProcessingService Properties (Local Computer)

| Log Onl<br>General     |                             | Recovery<br>Dependencies               |
|------------------------|-----------------------------|----------------------------------------|
| Log on as:             | This account:               | Allow service to interact with desktop |
| ◯ Local System account | serviceaccount@clientdomain | Browse                                 |
| Password:              |                             |                                        |
| Confirm password:      |                             |                                        |

## Sync360 Service Recovery Configuration

It is recommended to configure service auto restart in case of failure.

| Use4si.ProcessingService Properties (Local Computer)<br>Recovery<br>Dependencies<br>Log On<br>General<br>Select the computer's response if this service fails. Help me set up recovery<br>actions.<br>First failure:<br><b>Bestart the Service</b><br><b>Bestart the Service</b><br>Second failure:<br>Subsequent failures:<br><b>Restart the Service</b><br>Reset fail count after:<br>۵<br>days<br>5<br><b>Restart service after:</b><br>minutes |  |  |  |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--|--|--|
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                    |  |  |  |

# UPDATE

**CAUTION:** To update Sync360 1.24.0410 or newer, follow the standard installation procedure. For more info on installation, please refer to 'Installation via Graphic Interface'. We recommend creating backups before every update.

Unlike clean installation, update to version 1.24.0410 or newer from the old version of Sync360 requires manual actions. To update, do the following:

- 1. In your Windows Server system, navigate to the folder where Sync360 is currently installed, e.g. …\Program Files\Sync360
- 2. Backup the contents of the Sync360 folder.

| ۸<br>Date modified<br>Name<br>Type<br>15.04.2024 15:25<br>File folder<br><b>Applications</b><br>Config<br>15:04:2024 15:25<br>File folder<br>15:04:2024 15:25<br><b>ExProperties</b><br>File folder<br>Instances<br>15:04:2024 15:25<br>File folder<br>18.03.2024 20:05<br>File folder<br>Libranes<br>Logs<br>15:04.2024 0:03<br>File folder<br>File folder<br><b>Resources</b><br>15:04.2024 15:25<br><b>Scripts</b><br>15.04.2024 15:25<br>File folder<br>AutoCompleteSettings.xml<br>17.02.2023 16:53<br>XML File<br>Azure.Core.dll<br>04.11.2021 0:58<br>Application extens<br>ы<br>Azure.Identity.dll<br>14.10.2021 15:34<br>Application extens<br>[a]<br>Azure.Storage.Blobs.dll<br>09.09.2021 0:13<br>Application extens<br>Azure.Storage.Common.dll<br>M<br>09.09.2021 0:12<br>Application extens<br>Azure.Storage.Files.DataLake.dll<br>n<br>09.09.2021 0:13<br>Application extens<br>BAC_Use4si.Exchange.dil<br>м<br>04.12.2023 21:13<br>Application extens<br><b>BST.dll</b><br>ы<br>17.02.2023 16:53<br>Application extens<br>Castle.Core.dll<br>20.06.2018 16:07<br>Application extens<br>↓<br>backup > Sync360 > 240415 ><br>$\lambda$<br>Date modified<br>Name<br>Type<br>Size<br>15.04.2024 15:27<br>File folder<br>Applications<br>15:04:2024 15:27<br>File folder<br>Config<br>15:04.2024 15:27<br><b>ExProperties</b><br>File folder<br>15.04.2024 15:27<br>File folder<br>Instances<br>15.04.2024 15:27<br>File folder<br>Libranes<br>File folder<br><b>Resources</b><br>15:04.2024 15:27<br><b>Scripts</b><br>15.04.2024 15:27<br>File folder | $\checkmark$  |  | Program Files > Sync360 > |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|--|---------------------------|
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Size          |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 8 KB          |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 225 KB        |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <b>288 KR</b> |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 1 036 KB      |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 86 KB         |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 535 KB        |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 1099 KB       |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 425 KB        |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 425 KB        |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |               |  |                           |
| 17.02.2023 16:53<br>XML File<br>8 KB<br>AutoCompleteSettings.xml                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |               |  |                           |

| ExProperties                          | 15.04.2024 15:27 | File folder        |         |
|---------------------------------------|------------------|--------------------|---------|
| Instances                             | 15.04.2024 15:27 | File folder        |         |
| Libranes                              | 15.04.2024 15:27 | File folder        |         |
| <b>Resources</b>                      | 15.04.2024 15:27 | File folder        |         |
| <b>Scripts</b>                        | 15.04.2024 15:27 | File folder        |         |
| AutoCompleteSettings.xml              | 17.02.2023 16:53 | XML File           | 8 KB    |
| Azure.Core.dll                        | 04.11.2021 0:58  | Application extens | 225 KB  |
| Azure.Identity.dll                    | 14.10.2021 15:34 | Application extens | 288 KB  |
| Azure.Storage.Blobs.dll               | 09.09.2021 0:13  | Application extens | 036 KB  |
| 岡<br>Azure.Storage.Common.dll         | 09.09.2021 0:12  | Application extens | 86 KB   |
| Azure.Storage.Files.DataLake.dll<br>同 | 09.09.2021 0:13  | Application extens | 535 KB  |
| BAC Use4si,Exchange.dll<br>[e]        | 04.12.2023 21:13 | Application extens | 1099 KB |
| 岡<br><b>BST.dil</b>                   | 17.02.2023 16:53 | Application extens | 425 KB  |
| al Cartle Corp. 411                   | 20.06.2010 16-07 | Annibenting putper | ADE VO  |

**NOTE:** 'Logs' folder usually takes up a lot of space, so you would want to evaluate whether you need to back it up or not.

3. Navigate to 'Settings'  $→$  'Apps & Features' and uninstall Sync360.

| Settings                 |                                                                                                               | ×<br>□ |
|--------------------------|---------------------------------------------------------------------------------------------------------------|--------|
| Home<br>⋒                | Apps & features                                                                                               |        |
| Find a setting<br>$φ$    | Choose where to get apps                                                                                      |        |
| Apps                     | Installing apps only from Microsoft Store helps protect your device.                                          |        |
| IΕ<br>Apps & features    | Anywhere<br>$✓$                                                                                               |        |
| Б<br>Default apps        | Apps & features                                                                                               |        |
| Offline maps<br>吅        | Optional features                                                                                             |        |
| Apps for websites<br>$□$ | App execution aliases                                                                                         |        |
| Video playback<br>$□$    | Search, sort, and filter by drive. If you would like to uninstall or<br>move an app, select it from the list. |        |
| ç<br>Startup             | ø<br>sync                                                                                                     |        |
|                          | Sort by: Name $∨$<br>Filter by: All drives $⊃$<br>1 app found                                                 |        |
|                          | <b>Sync360</b><br>49.2 MB<br>Ń<br>04.12.2023<br>1.23.1204                                                     |        |
|                          | Modify<br><b>Uninstall</b>                                                                                    |        |

- 4. Navigate back to the Sync360 folder and manually delete the following files from that folder:
  - o Use4si.Develop.exe
  - o Use4si.ProcessingService.exe
  - o Use4si.WorkerProcess.exe
  - o Use4si.Develop.exe.config
  - o Use4si.ProcessingService.exe.config
  - o Use4si.WorkerProcess.exe.config
  - o Use4si.ProcessingService.InstallState

| Program Files<br>Sync360      |                  |                    | ्       | O |
|-------------------------------|------------------|--------------------|---------|---|
| ×<br>Name                     | Date modified    | <b>Type</b>        | Size    |   |
| Applications                  | 15.04.2024 15:28 | File folder        |         |   |
| Config                        | 15.04.2024 15:28 | File folder        |         |   |
| <b>ExProperties</b>           | 15.04.2024 15:28 | File folder        |         |   |
| Instances                     | 15.04.2024 15:28 | File folder        |         |   |
| Libraries                     | 18.03.2024 20:05 | File folder        |         |   |
| Logs                          | 15.04.2024 0:03  | File folder        |         |   |
| <b>Scripts</b>                | 15.04.2024 15:28 | File folder        |         |   |
| BAC Use4si.Exchange.dll       | 04.12.2023 21:13 | Application extens | 1099 KB |   |
| GmXmlParser.dll               | 30.01.2019 15:36 | Application extens | 11 KB   |   |
| old EmailParser.dll           | 18.11.2021 21:51 | Application extens | 269 KB  |   |
| old_System.ValueTuple.dll     | 19.03.2019 8:46  | Application extens | 29 KB   |   |
| old_Use4si.Infrastructure.dll | 28.05.2019 21:37 | Application extens | 61 KB   |   |
| Use4si.Scripting.Runtime.pdb  | 04.12.2023 21:20 | Program Debug D    | 360 KB  |   |
|                               |                  |                    |         |   |

**NOTE:** Any custom files left from the previous installation can be preserved if you still need them.

5. Locate the installer file, e.g., Sync360\_240410.msi, and launch the standard installation procedure. The new version is installed to the same Sync360 folder. For more info on installation, please refer to 'Installation via Graphic Interface'.

|                             |                                                                                                              | Sync360.Installer > Release |                          |                    | $\checkmark$ | O |
|-----------------------------|--------------------------------------------------------------------------------------------------------------|-----------------------------|--------------------------|--------------------|--------------|---|
| $\hat{\phantom{a}}$<br>Name | Date modified                                                                                                | Type                        | Size                     |                    |              |   |
| setup.exe                   | 10.04.2024 19:04                                                                                             | Application                 | 547 KB                   |                    |              |   |
| Sync360_240410.msi          | 10.04.2024 19:04                                                                                             | Windows Installer           | 17 582 KB                |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             | Sync360                                                                                                      |                             | $\overline{\phantom{0}}$ | $\Box$<br>$\times$ |              |   |
|                             | <b>Select Installation Folder</b>                                                                            |                             |                          |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             | The installer will install Sync360 to the following folder.                                                  |                             |                          |                    |              |   |
|                             | To install in this folder, click "Next". To install to a different folder, enter it below or click "Browse". |                             |                          |                    |              |   |
|                             | Folder:                                                                                                      |                             |                          |                    |              |   |
|                             | C\Program Files\Sync360\                                                                                     |                             |                          | Browse             |              |   |
|                             |                                                                                                              |                             |                          | Disk Cost          |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             |                                                                                                              |                             |                          |                    |              |   |
|                             |                                                                                                              | $8$ Back                    | Neg>                     | Cancel             |              |   |

6. After installation is complete, navigate to backup and copy 'Instances.xml' from the 'Applications' folder.

| Name                                | Date nodified    | Type               | Size           |
|-------------------------------------|------------------|--------------------|----------------|
| REL360_ContactSync_Belyaev          | 15.04.2024 15:27 | File folder        |                |
| REL360_ContactSync_Cleary_latest    | 15. 4.2024 15:27 | File folder        |                |
| REL360_ContactSync_customLinkStates | 15:27 15:27      | File folder        |                |
| REL360_ContactSync_DAVIS            | 5.04.2024 15:27  | File folder        |                |
| REL360_ContactSync_GIT              | 15.04.2024 15:27 | File folder        |                |
| REL360 ContactSync_NODES            | 15.04.2024 15:27 | File folder        |                |
| REL360_EmailWeight_GIT              | 15.04.2024 15:27 | File folder        |                |
| REL360 PotentialContact             | 15.04.2024 15:27 | File folder        |                |
| REL360_PotentialContact_arsene      | 15.04.2024 15:27 | File folder        |                |
| REL360_PotentialContact_CLEARY      | 15:04.2024 15:27 | File folder        |                |
| REL360 PotentialContact GIT         | 15:04.2024 15:27 | File folder        |                |
| REL360 PotentialContact OLD         | 15.04.2024 15:27 | File folder        |                |
| REL360 PotentialContact wildca ds   | 15.04.2024 15:27 | File folder        |                |
| SalesForce                          | 15.04.2024 15:27 | File folder        |                |
| Sample                              | 15.04.2024 15:27 | File folder        |                |
| SuffolkImport                       | 15.04.2024 15:27 | File folder        |                |
| Sync360LogArchive                   | 15.04.2024 15:27 | File folder        |                |
| SynchITFull                         | 15.04.2024 15:27 | File folder        |                |
| TaskProcessingSystem                | 15.04.2024 15:27 | File folder        |                |
| test                                | 15.04.2024 15:27 | File folder        |                |
| test111                             | 15.04.2024 15:27 | File folder        |                |
| testonline                          | 15.04.2024 15:27 | File folder        |                |
| tests                               | 15.04.2024 15:27 | File folder        |                |
| TestSynergyMichation                | 15.04.2024 15:27 | File folder        |                |
| <b>TfsContactSyn</b>                | 15.04.2024 15:27 | File folder        |                |
| <b>Transactiona</b> Upload          | 15.04.2024 15:27 | File folder        |                |
| <b>zzz Archive</b>                  | 15.04.2024 15:27 | File folder        |                |
| ContactSync-Scripts.zip             | 17.04.2020 18:38 | WinRAR ZIP archive | <b>98 KB</b>   |
| EmailWorldht_NEW_DB_MIGRATIONS.zip  | 28.09.2021 22:22 | WinRAR ZIP archive | 58 KB          |
| Instant es - Copy.xml               | 03.02.2022 17:40 | XML File           | 1 <sub>K</sub> |
| <b>Instances.xml</b>                | 27.03.2024 21:34 | XML File           | $1$ KB         |

7. Navigate to fresh installation and paste 'Instances.xml' to the 'Applications' folder.

| Program Files > Sync360 > Applications         |                  |                    |              |
|------------------------------------------------|------------------|--------------------|--------------|
| Name                                           | Date modified    | Type               | Size         |
| a<br>REL360_ContactSync_Belyaev                | 18.03.2024 20:05 | File folder        |              |
| REL360_ContactSync_Cleary_latest               | 18.03.2024 20:05 | File folder        |              |
| REL360_ContactSync_customLinkStates            | 18.03.2024 20:05 | File folder        |              |
| REL360_ContactSync_DAVIS                       | 18.03.2024 20:05 | File folder        |              |
| ٠<br>REL360 ContactSync GIT                    | 18.03.2024 20:05 | File folder        |              |
| REL360 ContactSync NODES                       | 18.03.2024 20:05 | File folder        |              |
| REL360 EmailWeight GIT                         | 18.03.2024 20:05 | File folder        |              |
| REL360 PotentialContact                        | 18.03.2024 20:05 | File folder        |              |
| REL360_PotentialContact_arsene                 | 18.03.2024 20:05 | File folder        |              |
| REL360_PotentialContact_CLEARY                 | 18.03.2024 20:05 | File folder        |              |
| REL360_PotentialContact_GIT                    | 18.03.2024 20:05 | File folder        |              |
| REL360 PotentialContact OLD                    | 18.03.2024 20:05 | File folder        |              |
| REL360_PotentialContact_wild <sup>c</sup> ards | 18.03.2024 20:05 | File folder        |              |
| SalesForce                                     | 18.03.2024 20:05 | File folder        |              |
| Sample                                         | 18.03.2024 20:05 | File folder        |              |
| SuffolkImport                                  | 18.03.2024 20:05 | File folder        |              |
| Sync360LogArchive                              | 15.04.2024 15:31 | File folder        |              |
| SynchiTFull                                    | 18.03.2024 20:05 | File folder        |              |
| TaskProcessingSystem                           | 18.03.2024 20:05 | File folder        |              |
| test                                           | 18.03.2024 20:05 | File folder        |              |
| test111                                        | 18.03.2024 20:05 | File folder        |              |
| testonline                                     | 18.03.2024 20:05 | File folder        |              |
| tests                                          | 18.03.2024 20:05 | File folder        |              |
| TestSynergyMig ation                           | 18.03.2024 20:05 | File folder        |              |
| <b>TfsContactSync</b>                          | 18.03.2024 20:05 | File folder        |              |
| <b>Transactional Joload</b>                    | 18.03.2024 20:05 | File folder        |              |
| zzz Archive                                    | 18.03.2024 20:05 | File folder        |              |
| ContactSync-Scripts.zip                        | 17.04.2020 18:38 | WinRAR ZIP archive | <b>GR KR</b> |
| EmailWeinht_NEW_DB_MIGRATIONS.zip              | 28.09.2021 22:22 | WinRAR ZIP archive | 58 KB        |
| Instance - Copy.xml                            | 03.02.2022 17:40 | XML File           | $1$ KB       |
| Instances.xml                                  | 27.03.2024 21:34 | XML File           | <b>1 KB</b>  |

8. Open 'Apps & Features' and check the version of an updated Sync360.Settings

| Home<br>⋒                   | Apps & features                                                                                               |                                                                                      |
|-----------------------------|---------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| Find a setting<br>$\varphi$ | Choose where to get apps                                                                                      | <b>Related settings</b><br>Programs and Features                                     |
| Apps                        | Installing apps only from Microsoft Store helps protect your device.                                          |                                                                                      |
| 拒<br>Apps & features        | Anywhere<br>$\checkmark$                                                                                      | Help from the web                                                                    |
| 長<br>Default apps           | Apps & features                                                                                               | Finding App or Program installed in<br>Windows<br>Installing web apps from Microsoft |
| Offline maps<br>血           | Optional features                                                                                             | Turning off app recommendations                                                      |
| Apps for websites<br>$\Box$ | App execution aliases                                                                                         | Repairing apps and programs                                                          |
| Video playback<br>$\Box$    | Search, sort, and filter by drive. If you would like to uninstall or<br>move an app, select it from the list. | ⋒<br>Get help                                                                        |
| ç<br>Startup                | D<br>sync                                                                                                     | Give feedback<br>£                                                                   |
|                             | Filter by: All drives $\vee$<br>Sort by: Name $\vee$                                                          |                                                                                      |
|                             | 1 app found                                                                                                   |                                                                                      |
|                             | Sync360<br>50,4 MB<br>¦¦Ş<br>15.04.2024<br>1.24.0410                                                          |                                                                                      |
|                             | Modify<br>Uninstall                                                                                           |                                                                                      |

 $\Box$  $\times$ 

## **STRUCTURE**

After the successful installation of Sync360, let's take a look at the program folder structure.

- Sync360 main folder
  - Applications folder for logical applications
    - $\triangleright$  **O** System system application
    - Sync360 application bundled with engine
      - $\blacksquare$  Config - Contains configuration for application
      - $\blacksquare$  Instances  $-$  Contains scheduler for application
      - **A** Scripts - Contains application scripts
        - Oprivate Special folder for subscripts
        - $\blacksquare$  Ranges Contains application processing object
    - **Config** - Contains default configuration file
  - 4 Customization Contains solution file for CRM

UserSynchronizationLog\_1\_0\_161213\_managed.zip

- Default exchange properties **ExProperties**
- Default scheduler configuration Instances
- Default logs folder Logs
- **Scripts** - Default scripts folder

Here's another presentation of the folder structure.

#### **Sync360 Engine Folder Structure**

Image /page/12/Figure/20 description: A pie chart displaying the distribution of favorite ice cream flavors among a group of participants. The chart is labeled 'Favorite Ice Cream Flavors' at the top. The slices represent different flavors, with pink representing vanilla, blue for chocolate, and green for mint. The percentages shown next to each flavor are as follows: vanilla (30%), chocolate (25%), and mint (15%).

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

Image /page/13/Picture/14 description: The image depicts a serene landscape featuring a clear blue sky with fluffy clouds, a vibrant green meadow sprawling as far as the eye can see, and a winding river gracefully flowing through the middle of it all. In the foreground, there is a small wooden bridge spanning across the river, adding to the picturesque setting.

### Applications Folder

The **Sync360\Applications** folder, as the name implies, contains applications. In Sync360 there is a logical concept of Applications. Each folder within the **Sync360\Applications** folder is a separate application with its own set of configs, instances and scripts.

By default, the **Sync360\Applications** folder contains two predefined applications:

- **@System**—is used by engine and should not be modified.
- **Sync360LogArchive**—this application gathers and stores synchronization logs.

Image /page/14/Picture/5 description: A pie chart is displayed with a title 'Annual Sales Distribution'. The sectors of the pie chart represent different product categories such as Electronics, Clothing, Home Appliances, and Sporting Goods. The percentages indicated are 30% for Electronics, 25% for Clothing, 20% for Home Appliances, and 25% for Sporting Goods.

Sync360LogArchive

a Instances.xml

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
        \langle add\rangle </schedule>
</config>
```

### Scripts Folder

The **Sync360\Applications\[name of application 1]\Scripts** folder contains all application scripts, which perform data processing, and two subfolders **@Private** and **Ranges.**

- **Oprivate**
- Ranges
- CleanCrmContacts
- <sup>ca</sup> CleanExchangeContacts
- <sup>(e3)</sup> CreateNewContactsInCrm
- PreCreateNewContactsInCrm
- ShakeFrozenExchangeContacts
- SyncExistingContacts
  - **@Private** this is a special folder where the engine tries to find scripts which are invoked by the Call operator.
  - **Ranges** this folder is used by scheduler to locate scripts that define objects for processing.

#### Ranges Folder

The folder contains the script that defines ObjectsForProcessing. The script is referenced by the scheduler script through the Criteria parameter and  $\circ$ bjectIds are passed down to application scripts for record processing.

```
For example, the script may be called SyncEmployees.xml and contain an object for employee records:
<script>
    <select from="crm" entity="vs360_employee" into="employees">
        <query>
        <![CDATA[
            <fetch version="1.0" output-format="xml-platform" mapping="logical" 
distinct="true">
              <entity name="vs360_employee">
                <attribute name="vs360_employeeid" />
                <filter type="and">
                  <condition attribute="statecode" operator="eq" value="0" />
                </filter>
                <link-entity name="vs360_vs360_employee_team" 
from="vs360_employeeid" to="vs360_employeeid" visible="false" intersect="true">
                  <link-entity name="team" from="teamid" to="teamid" alias="aa">
                    <filter type="and">
                       <condition attribute="name" operator="eq" 
value="SyncEmployees" />
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
        <set 
var="ObjectsForProcessing[]">{employee.vs360_employeeid.ToString()}</set>
   \langle /for>
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
   <var Employee='["EmployeeId" : (Input.ObjectId ?? "E4340742-XXXX-
0C295CA5D3")]'/>
   <var SyncState="new Object()"/>
```

# EXECUTION

During the script execution the engine does the following:

- 1. Tries to find the **[instanceconfiguration].xml** file in the **Sync360\Applications\[name of application 1]\Instances** folder. If it has not been found there, it will check for this file in the **Sync360\Instances** folder, and if nothing has been found there too, the scheduler configuration will be skipped.
- 2. Reads the **Sync360\Config\default.xml** file, then reads the **Sync360\Applications\[name of application 1]\Config\default.xml** file and finally reads the **Sync360\Applications\[name of application 1]\Config\[datasourceconfiguration].xml** file. All configuration files are combined into one, in predefined order.

Based on this example, let's take a look at what the engine will do at the runtime:

- 1. Try to find the **default.xml** file in the **Sync360\Applications\ContactSync\Instances** folder. If it's not found it will check for this file in the **Sync360\Instances** folder, and if it's not found there too, the scheduler configuration will be skipped.
- 2. Read the **Sync360\Config\default.xml** file, then read the **Sync360\Applications\ContactSync\Config\default.xml** file and finally read the **Sync360\Applications\ContactSync\Config\dev.xml** file. All configuration files will be combined into one, in predefined order.

# USING ENGINE

You can also create your own application.

- 1. Copy the **Sync360\Applications\Sync360LogArchive** folder and paste it to the same folder.
- 2. Give it a name, for example, **MyApp**:
  - **O**System MyApp  $Sync360$ sa Instances.xml
- 3. Return to the main **Sync360** folder. The next step will be editing the files and running the scripts using the **Use4si.Develop.exe:**

Image /page/21/Picture/6 description: A group of individuals filling out a survey about their preferences for different types of fruits are shown. The survey is titled 'Fruit Preference Survey'. Across the top row, there are three columns labeled Apples, Bananas, and Oranges, each with corresponding numeric values below them. At the bottom left corner, it says '20' next to apples, at the bottom middle it says '15' next to bananas, and at the bottom right it says '10' next to oranges.

**Use4si.Develop.exe** is an .exe file that allows user to execute scripts through the developer interface.

| Sync360 Develop                               |                                                   |                             |                          |
|-----------------------------------------------|---------------------------------------------------|-----------------------------|--------------------------|
| File Edit View Run Tools Help                 |                                                   |                             |                          |
| DDBFF XDB IO   Instance Id                    | $\bigodot$ Run $\bigcirc$ $\bigcirc$ $\mathbb{X}$ |                             |                          |
| Untitled 1.xml                                | $\bullet$ $\times$                                | <b>Application Explorer</b> | $\upmu$ $\times$         |
| $1\,$                                         |                                                   | $+ \cdot \mathbb{C}$        | $\overline{\phantom{a}}$ |
| Output                                        | $\upmu$ $\times$                                  |                             |                          |
| $\mathbf{1}$                                  |                                                   |                             |                          |
| <b>E</b> Breakpoints   Match   Match   Output |                                                   |                             |                          |

During installation a windows service is created, it's used for processing Applications as described in Configuration section. You can change the start state to automatic after all required preparation is done.

| Services (Local)                               |                          |                      |                      |
|------------------------------------------------|--------------------------|----------------------|----------------------|
| <b>Use4si.ProcessingService</b>                | ∧<br>Name                | Startup Ty Log On As |                      |
| Stop the service<br><b>Restart the service</b> | UPnP Device Host         | Manual               | <b>Local Service</b> |
|                                                | Use4si.ProcessingService | Automatic            | Local System         |
|                                                | User Data Access_46279   | Manual               | Local System         |
| Standard<br>Extended                           |                          |                      |                      |

You can also find an .exe file **Use4si.Develop.exe** in the main Sync360 folder and run it as administrator.

| Name                    | Size<br>Type         |
|-------------------------|----------------------|
| Applications            | File folder          |
| Config                  | File folder          |
| Customization           | File folder          |
| ExProperties            | File folder          |
| Instances               | File folder          |
| Logs                    | File folder          |
| Scripts                 | File folder          |
| Use4si.Develop          | 4.40.1/0             |
| Use4si.InstallerCustomA | Open                 |
| Use4si.ProcessingServid | Run as administrator |

An **example** of script execution:

Image /page/23/Figure/1 description: A pie chart displaying the distribution of favorite pizza toppings. The chart is titled 'Pizza Toppings Survey' and features several slices representing different toppings such as Pepperoni, Mushroom, and Cheese. Each slice has a label indicating the percentage of people who prefer that topping.

| First Name                                                                                                                                                                                                                                                                                                                          | Last Name | Age |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|-----|
| John                                                                                                                                                                                                                                                                                                                                | Doe       | 25  |
| Output 4 ERROR System. Exception: Error at CreateStatement in $\mathbf{\mu} \times$ ⋥∧ C:\Program Files\Sync360\Applications\Sync360\Scripts\@private\StartSynchonization.xml ---> Use4si.Core.UnknownServiceException: Service with name or type data.connections.crm does not exist ---> Watch 4 Output Ln 11 Col 43 $\therefore$ |           |     |

#### Let's take a look on **the main features**:

| File                                                                                               | Edit<br>View<br>Run<br>New<br>Open                                                                                                                 |
|----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Untitled 2.xml<br>CleanCrmContacts.xml                                                             | You can edit multiple scripts                                                                                                                      |
| Instance Id                                                                                        | Put a string in the format<br>"instanceconfiguration.nameofapplication@datasourcec<br>onfiguration" (for example, sample@main) into the<br>textbox |
| (D) Run                                                                                            | Press to run the script                                                                                                                            |
|                                                                                                    | Press to pause/stop the script                                                                                                                     |
| ×                                                                                                  | Clear cache from last execution                                                                                                                    |
| $4 	imes$<br><b>Application Explorer</b><br>+ ∙ 2                                                  | Navigate and select application using the engine's<br>interface.                                                                                   |
| <b>Breakpoints</b><br>Condition<br>Name<br><b>Breakpoints 4</b> Watch <b>4</b> Output              |                                                                                                                                                    |
| Watch<br>Value<br>Name<br>User<br>Watch 4 Output                                                   | Here you can define variables to watch during the script<br>execution                                                                              |
| ņ.<br>Output<br>4 ERROR System. Exception:<br>₽<br>Error at CreateStatement<br>⋥<br>Watch 4 Output | Output window shows script execution results                                                                                                       |