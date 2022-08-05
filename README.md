# AS-Armis-Integration

Author: Accelerynt

For any technical questions, please contact info@accelerynt.com  

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Armis-Integration%2Fmain%2Fazuredeploy.json)
[![Deploy to Azure Gov](https://aka.ms/deploytoazuregovbutton)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Armis-Integration%2Fmain%2Fazuredeploy.json)       

This playbook is intended to be run from Microsoft Sentinel. It will ingest Armis Alerts on a rolling schedule and add them to the Custom Logs in your Microsoft Log Analytics Workspace.
                                                                                                                                     
![Armis_Demo](Images/Armis_Demo.png)

### Set Up

Before deploying this playbook, your Armis Secret Key, which will be used to obtain access tokens via the Armis API, must be stored in an Azure Key Vault.

Navigate to the Azure Key Vaults page: https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults

Navigate to an existing Key Vault or create a new one. From the Key Vault overview page, click the "**Secrets**" menu option, found under the "**Settings**" section.

![Armis_SetUp_1](Images/Armis_SetUp_1.png)

Click "**Generate/Import**".

![Armis_SetUp_2](Images/Armis_SetUp_2.png)

Choose a name for the secret and enter the Armis Secret Key in the "**Value**" field. All other settings can be left as is. 

![Armis_SetUp_3](Images/Armis_SetUp_3.png)

Click "**Create**". 

Once your secret has been added to the vault, navigate to the "**Access policies**" menu option, also found under the "**Settings**" section on the Key Vault page menu. Leave this page open, as you will need to return to it once the playbook has been deployed. See [Granting Access to Azure Key Vault](https://github.com/Accelerynt-Security/AS-Armis-Integration#granting-access-to-azure-key-vault).

![Armis_SetUp_4](Images/Armis_SetUp_4.png)
                                                                                                 
#
### Deployment                                                                                                         
                                                                                                        
To configure and deploy this playbook:
 
Open your browser and ensure you are logged into your Microsoft Sentinel workspace. In a separate tab, open the link to our playbook on the Accelerynt Security GitHub Repository:

https://github.com/Accelerynt-Security/AS-Armis-Integration

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Armis-Integration%2Fmain%2Fazuredeploy.json)
[![Deploy to Azure Gov](https://aka.ms/deploytoazuregovbutton)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Armis-Integration%2Fmain%2Fazuredeploy.json)                                             

Click the “**Deploy to Azure**” button at the bottom and it will bring you to the custom deployment template.

In the **Project Details** section:

* Select the “**Subscription**” and “**Resource Group**” from the dropdown boxes you would like the playbook deployed to.  

In the **Instance Details** section:   

* **Playbook Name**: This can be left as “**AS-Armis-Integration**”, or you may change it.  

* **Armis Org**: This is the subdomain in the URL of your Armis org. For example, for the URL **https://[myorg].armis.com**, you would enter "**myorg**" in this field.

* **Key Vault Name**: This is the name of the Azure Key Vault that you have your Armis Secret Key stored in.  

* **Secret Name**: This is the name of the Azure Key Vault Secret container that holds the value of your Armis Secret Key.  

Towards the bottom of the page, click on “**Review + create**”. 

![Armis_Deploy_1](Images/Armis_Deploy_1.png)

Once the resources have validated, click on "**Create**".

![Armis_Deploy_2](Images/Armis_Deploy_2.png)

The resources should take around a minute to deploy. Once the deployment is complete, you can expand the "**Deployment details**" section to view them.
Click the one corresponding to the Logic App.

![Armis_Deploy_3](Images/Armis_Deploy_3.png)

Click on the “**Edit**” button. This will bring you into the Logic Apps Designer.

![Armis_Deploy_4](Images/Armis_Deploy_4.png)

Before the Logic App can be run, a connection nested in the final step of the Logic App will either need to be authorized, or an existing authorized connection may be alternatively selected. Expand the final step labeled "**Condition**" and then the subsequent "**For Each**" step.

![Armis_Deploy_5](Images/Armis_Deploy_5.png)

To validate the connection created for this Logic App, expand the "**Connections**" step and click the exclamation point icon next to the name matching the Logic App.
                                                                                                
![Armis_Deploy_6](Images/Armis_Deploy_6.png)

This will bring up an interface for connecting to the Log Analytics Workspace that will house your Armis Alert Logs. The connection name should be set to "**ArmisIntegration**", or something similar. This will be the custom log name and follows strict naming conventions; spaces and special characters (including dashes) are not permitted.

![Armis_Deploy_7](Images/Armis_Deploy_7.png)                                                                                                                                                                                                                                                   
To retrieve your workspace key and id, open a separate tab and navigate to the Microsoft Sentinel page: 
https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/microsoft.securityinsightsarg%2Fsentinel

Select the desired workspace. From there, click on "**Settings**". Finally, click the "**Workspace settings**" tab.

![Armis_Deploy_8](Images/Armis_Deploy_8.png)  

This will bring you to the overview of the Log Analytics workspace. Under the "**Settings**" category of the menu, click "**Agents management**".

![Armis_Deploy_9](Images/Armis_Deploy_9.png)  

Here you can copy your "**Workspace ID**" and either your "**Primary key**" or "**Secondary key**". If your primary and secondary keys are no longer visible, it may be best to create a new workspace, as regenerating these keys could break existing connections.

![Armis_Deploy_10](Images/Armis_Deploy_10.png)

Returning to the "**Logic Apps Designer**" (edit) screen, paste the “**Workspace ID**" in the corresponding field and either your "**Primary key**" or "**Secondary key**" value in the "**Workspace Key**" field. Then click "**Update**".

![Armis_Deploy_11](Images/Armis_Deploy_11.png)

#
### Granting Access to Azure Key Vault

Before the Logic App can run successfully, the keyvault connection created during deployment must be granted access to the Key Vault storing your Armis Secret Key.

From the Key Vault "**Access policies**" page, click "**Add Access Policy**".

![Armis_Access_1](Images/Armis_Access_1.png)

Select the "**Get**" checkbox in the "**Secret permissions**" list field. Then click the blue "**None selected**" text next to the "**Select principal**" field.

Paste "**AS-Armis-Integration**" into the principal search box and click the option that appears. Click "**Select**" towards the bottom of the page.

![Armis_Access_2](Images/Armis_Access_2.png)

Click "**Add**".

![Armis_Access_3](Images/Armis_Access_3.png)
