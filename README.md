# DefenderForServersMappingToMDETag
> Summary
This template provides to write tag as Azure subscription name in Defender XDR for Azure VM installed Defender for Servers (MDE).<BR>
This template percreate MDE tag by prerioLogic apps periodically
 - Tag name "DefenderForServers"
 - Tag name ``Azure Subscription Name`` from Azure Resource Graph query

For Japanese README is here.
![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/788710e3-d3b3-45f7-b98a-118a9359204f)

# Deploy To Azure
> Let's install ARM template!
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FDefenderForServersMappingToMDETag%2Fmain%2FDefenderForServersMappingToMDETag.json)

# Configure the logic apps
> After deploy to Azure, you need to configure the following steps

- You can tune "Reccurence" duration for the first steps. Initial parameter sets to 1 month for reccurence, so you can tune this parameter.
- You should configure ``Reader`` role for system assigned identity
 - This logic app polls query to post Azure Resource Graph by system assigned managed identity

![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/cf976a86-33be-45cf-b647-62b05d907ccb)
![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/aa29bd6a-2683-4217-8786-6c694c15de7e)

# Check the process
> You can check logicapps manually, then two tags will be set to each devices for endpoint in Defende XDR

After the tuning, let's start logic apps manually. If the configuration is succeeded, two Tags ``DefenderForServers`` and ``<Azure Subscription Name>`` will be set to each devices.

![image](https://github.com/hisashin0728/DefenderForServersMappingToMDETag/assets/55295601/82c0f813-e5e6-4112-9308-a6968958c527)
