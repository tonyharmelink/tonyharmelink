+++
date = "2019-09-23T21:31:00+00:00"
tags = []
title = "Extract VMware Horizon Client MSI in 5.1"

+++
I was working on extracting the MSI for the Horizon Client and noticed that the /x and /extract flags on the [VMware Horizon client documentation](https://docs.vmware.com/en/VMware-Horizon-Client-for-Windows/5.2/horizon-client-windows-installation/GUID-1DCFA762-6F08-455D-82C0-2A18C87E41D5.html "Docs") weren't working for me.

Luckily I did notice a few lines in the log of the installer in my %temp% directory

    [6D2C:6D30][2019-09-23T16:11:42]i410: Variable: WixBundleAction = 5
    [6D2C:6D30][2019-09-23T16:11:42]i410: Variable: WixBundleElevated = 1
    [6D2C:6D30][2019-09-23T16:11:42]i410: Variable: WixBundleInstalled = 1

It's a Wix Bundle installer. These I know how to deal with after exploding and customizing other installers.

First, download the [Wix Toolset](https://wixtoolset.org/releases/ "Wix Toolset")

Then, use the WIX installer toolkit to extract the MSI from the bundle. your paths may be different.

    & 'C:\Program Files (x86)\WiX Toolset v3.11\bin\dark.exe' C:\Temp\VMware-Horizon-Client-5.1.0-14045148.exe -x .\Horizon_Extracted

Now, within the extracted folder you have UX and AttachedContainer folders. The AttachedContainer has the subcomponent installers and bundles of the full exe.

Pick and choose what you need and enjoy.

Addendum: The builtin extract did work properly in the 5.2.0 bundle.