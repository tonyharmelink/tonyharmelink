+++
date = "2019-09-01T07:24:00+00:00"
tags = ["Workspace ONE", "Windows 10"]
title = "Workspace ONE: Remove APPX packages from Windows 10 with a custom CSP"

+++
Here is a full CSP method to removing APPX packages from your users environment.

First, get the family names of the packages you want to remove via powershell

    Get-AppxPackage | Select Name, PackageFamilyName

![](/uploads/2019-09-01 10_28_59-Window.png)

Determine the packages you wish to remove and add those names into the CSP similar to below, change the CmdID to be unique within your configuration:

    <Delete>
       <CmdID>105</CmdID>
       <Item>
          <Target>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.Messaging_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.MixedReality.Portal_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.NetworkSpeedTest_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.WindowsMaps_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.Xbox.TCUI_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxApp_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxGameCallableUI_cw5n1h2txyewy</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxGameOverlay_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxGamingOverlay_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxIdentityProvider_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.XboxSpeechToTextOverlay_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.YourPhone_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.ZuneMusic_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/Microsoft.ZuneVideo_8wekyb3d8bbwe</LocURI>
             <LocURI>./Device/Vendor/MSFT/EnterpriseModernAppManagement/AppManagement/AppStore/microsoft.windowscommunicationsapps_8wekyb3d8bbwe</LocURI>
          </Target>
       </Item>
    </Delete>

Create a custom Profile in VMware Workspace ONE with the newly built CSP.

![](/uploads/CustomCSP.png)

The caveat to this CSP is that it only applies to new logins on the applied machines. Existing users will still see the 'removed' applications in their start menu.

Go through your normal testing and deployment.

The RemovePackage part of the [EnterpriseModernAppManagement CSP](https://docs.microsoft.com/en-us/windows/client-management/mdm/enterprisemodernappmanagement-csp "Giant Thing") does seem to be better in the long run, perhaps without the caveat posted above, but from reading and testing we can't run Exec CSPs. If you have other information, this changes, or you have another solution via CSP message me on twitter.

Enjoy!