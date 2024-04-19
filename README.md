# DefenderForServersMappingToMDETag
> Summary

This repository ``DefenderForServersMappingToMDETag`` provides a ARM template for writing tags as Azure subscription names for Azure Virtual Machines (VMs) that have Defender for Servers (MDE) installed. This ARM template periodically creates MDE tags using Logic Apps. <p>

This template provides to write tag as Azure subscription name in Defender XDR for Azure VM installed Defender for Servers (MDE).<BR>
This template percreate MDE tag by Logic apps periodically
 - Tag name ``DefenderForServers``
 - Tag name ``<Your Azure Subscription Name>`` from query result of Azure Resource Graph

For [Japanese README.md is here](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/blob/main/READMEJP.md).
![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/788710e3-d3b3-45f7-b98a-118a9359204f)

# Deploy To Azure
> Let's install ARM template!

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FDefenderForServersMappingToMDETag%2Fmain%2FDefenderForServersMappingToMDETag.json)

# Configure the Logic apps
> After deploy to Azure, you need to configure the following steps

- You can modify tuning "Reccurence" duration for the first steps.
  - Initial parameter sets to ``1`` month for reccurence. You can modify this parameter if you want.
- You should configure multiple Subscription ``Reader`` roles for system assigned identity
  - This logic app polls query to post Azure Resource Graph by system assigned managed identity
  - If you have multiple subscriptions and deploys Defender for Servers to Azure VMs on each subscriptions, the logic app requires each subscription ``reader`` role for managed identity.

![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/cf976a86-33be-45cf-b647-62b05d907ccb)
![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/aa29bd6a-2683-4217-8786-6c694c15de7e)

# Check the process
> You can check logicapps manually, then two tags will be set to each devices for endpoint in Defende XDR

After the tuning, let's start logic apps manually. If the configuration is succeeded, two Tags ``DefenderForServers`` and ``<Azure Subscription Name>`` will be set to each devices.

<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/82c0f813-e5e6-4112-9308-a6968958c527">

# Notes

- This logic apps first query to all device resources that is filtered in Defender XDR
  - onboardingStatus is ``Onboarded``
  - healthStatus is ``Active``
  - **NOT** machineTags equal ``DefenderForServers`` <-- Initially, Tag will be embedded, but secondary process ignored 
  - **NOT** osPlatform eq ``Windows10`` or osPlatform eq ``Windows11`` 

<img alt="hogehoge" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/3433ea73-29a5-4f0c-bcb9-b7546d393c70" width="800"><BR>

- This logics apps pickup subscription id from Resource ID information from Defender XDR
<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/b84f570a-f0cb-405b-86db-3f9210b03ef3">

 Some devices are NOT embbeded ``subscriptionId`` in Defender XDR, so this templates pickup ``resourceId``
<img width="800" alt="image" src="https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/5ed72e1b-7268-46d5-9bec-0b30ad98283d">

