![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png)

<div class="MCWHeader1">
Azure Stack
</div>

<div class="MCWHeader2">
Before the hands-on lab setup guide
</div>

<div class="MCWHeader3">
June 2019
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2019 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.# Azure Stack setup

**Contents**

<!-- TOC -->

- [Azure Stack before the hands-on lab setup guide](#azure-stack-before-the-hands-on-lab-setup-guide)
    - [Requirements](#requirements)
    - [Before the hands-on lab](#before-the-hands-on-lab)
        - [Task 1: Create a virtual machine to execute the lab](#task-1-create-a-virtual-machine-to-execute-the-lab)
        - [Task 2: Install the Azure Stack Developer Kit](#task-2-install-the-azure-stack-developer-kit)
        - [Task 3: Download and Run the Azure Stack Configurator Script](#task-3-download-and-run-the-azure-stack-configurator-script)

<!-- /TOC -->

# Azure Stack before the hands-on lab setup guide 

## Requirements

-   Microsoft Azure subscription 

## Before the hands-on lab

Duration: 8-10 hours

To execute this lab, you will have two options: you can use your own Azure Stack Development Kit that is already installed, or you will need to follow the instructions below to setup your own in Microsoft Azure using nested virtual machines.

For help with installation of the Azure Stack Development Kit, review the following article: <https://azure.microsoft.com/en-us/overview/azure-stack/development-kit>.

### Task 1: Create a virtual machine to execute the lab

1. Click the Deploy to Azure Button to create the Azure Stack Host VM in your Azure subscription.

   [![Deploy to Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FMCW-Azure-Stack%2Fmaster%2FHands-on%20lab%2ftemplate%2fazuredeploy.json)


2.  Specify a resource group name **AzureStack** and deploy the template.

    > **Note:** Please wait for the virtual machine to be provisioned prior to moving to the next step.

3.  After the VM is provisioned, **connect** to establish a new Remote Desktop Session.

4.  Depending on your Remote Desktop Protocol Client and browser configuration, you will either be prompted to open an RDP file, or you will need to download it and then open it separately to connect.

5.  Log in with the credentials specified during creation:

    -   User: **administrator**

    -   Password: **demo\@pass123**

6.  You will be presented with a Remote Desktop Connection warning because of a certificate trust issue. Click **Yes** to continue with the connection.

    ![Screenshot of the Remote Desktop Connection warning dialog box.](images/Setup/image3.png)

7.  When logging on for the first time, you will see a prompt on the right asking about network discovery. Click **No**.

    ![Screenshot of the Network discovery prompt, with the No button selected.](images/Setup/image4.png)

8.  Notice that Server Manager opens by default. On the left, click **Local Server**.

    ![Local Server is selected in the Server Manager menu.](images/Setup/image5.png)

9.  On the right side of the pane, click **On** by **IE Enhanced Security Configuration**.

10. Change to **Off** for Administrators and click **OK**.

    ![In the Internet Explorer Enhanced Security Configuration dialog box, Administrators is set to Off.](images/Setup/image7.png)

11. Launch Internet Explorer, and right click at the top of the browser and check Menu bar. Then open Tools -\> Internet Options -\> Security -\> Custom Level. Change File download to **Enable**.

    ![Under Downloads, File download is set to Enable.](images/Setup/image8.png)

### Task 2: Install the Azure Stack Developer Kit

1.  Launch an elevated PowerShell console and execute the following commands:

    ```powershell
    cd C:\AzureStackOnAzureVM

    .\Install-ASDK.ps1
    ```

    When prompted enter:

    - Enter **AAD** for the deployment type

    - Password for administrator: **demo\@pass123**

    - Enter the Azure AD User: Specify your Azure subscription user account.

    - Select ASK Version **1905-40** and press **C** to continue.

2.  It will take up to 6 hours to successfully install the Azure Stack developer kit.

3.  After the installation reboots the virtual machine, login to the **AzSHost-1** virtual machine using RDP to monitor the installation progress. You will need to use the account:

    -   Username: **azurestack.local\\azurestackadmin**

    -   Password: **\[your Azure Stack password\]**

4.  Once connected open Server Manager. On the left, click **Local Server**.  

    > **Note:** Wait until installation is complete.

### Task 3: Download and Run the Azure Stack Configurator Script

In this task you will execute a script that will configure Azure Stack with many of the standard Resource Providers such as SQL, App Service, and MySQL.

1. From within the **AzSHost-1** virtual machine launch an elevated PowerShell console.

2. The script requires the Windows 2016 Evaluation ISO for the Azure Stack Images. Download the ISO with the following command:

    ```PowerShell
    Start-BitsTransfer -Source https://cloudworkshop.blob.core.windows.net/azure-stack/iso/win2016eval.iso -Destination D:\win2016eval.iso
    ```
3. Download the ConfigASDK Script:

    ```PowerShell
    # Create directory on the root drive.
    New-Item -ItemType Directory -Force -Path "C:\ConfigASDK"
    Set-Location "C:\ConfigASDK"

    # Download the ConfigASDK Script.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Invoke-Webrequest http://bit.ly/configasdk -UseBasicParsing -OutFile ConfigASDK.ps1
    ```

4. Execute the script to configure the Azure SDK (ensure you replace the [placeholder values]). Note: This step will take between 3 and 4 hours.

    
    ```PowerShell
    .\ConfigASDK.ps1 -azureDirectoryTenantName "[tenant].onmicrosoft.com" -authenticationType AzureAD `
    -downloadPath "D:\ASDKINSTALL" -ISOPath "D:\win2016eval.iso" -azureStackAdminPwd 'demo@pass123' `
    -VMpwd 'demo@pass123' -azureAdUsername "[youruser]@[tenant].onmicrosoft.com" -azureAdPwd '[your user password]' `
    -registerASDK -useAzureCredsForRegistration -azureRegSubId "[Your Azure Subscription ID]"
    ```
