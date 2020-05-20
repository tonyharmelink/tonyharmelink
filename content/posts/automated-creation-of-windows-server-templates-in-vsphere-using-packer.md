+++
date = ""
draft = true
tags = []
title = "Automated creation of Windows Server templates in vSphere using Packer"

+++
I have a dream. A dream of turning everything in a Windows & VMware environment into code. Consistent, repeatable, code documented systems that can be destroyed, replaced, updated, and scaled. Moving from systems being carefully tuned and setup pets into expendable cattle. We see this with Linux all over the place, but not that much with Windows running on VMware.

VMware Cloud Foundation is one large step towards that vision, it handles the SDDC stack with ease. I am focusing more on the Windows side and starting with automated template creation.

After looking around at the tools available I settled on [Hashicorp Packer](https://www.packer.io/ "https://www.packer.io/") to build my templates. It allows consistent building from an `*.iso` and with the addition of the [Jetbrains vSphere Builder](https://github.com/jetbrains-infra/packer-builder-vsphere/ "https://github.com/jetbrains-infra/packer-builder-vsphere/") allows creation directly using the vSphere API. For more information I recommend [this article](https://www.thehumblelab.com/automating-ubuntu-18-packer/ "https://www.thehumblelab.com/automating-ubuntu-18-packer/") by [@Codydearkland](https://twitter.com/Codydearkland) on using Packer to build an Ubuntu 18.04 template.

### Preparing to build

You'll need 6 things:

* A clone if the git repository - [https://github.com/tonyharmelink/vsphere-packer-windows](https://github.com/tonyharmelink/vsphere-packer-windows "https://github.com/tonyharmelink/vsphere-packer-windows")
* A copy of Hashicorp Packer - [https://www.packer.io/downloads.html](https://www.packer.io/downloads.html "https://www.packer.io/downloads.html")
* The JetBrains vSphere builder - [https://github.com/jetbrains-infra/packer-builder-vsphere/releases](https://github.com/jetbrains-infra/packer-builder-vsphere/releases "https://github.com/jetbrains-infra/packer-builder-vsphere/releases")
* The Windows Update provisioner - [https://github.com/rgl/packer-provisioner-windows-update/releases](https://github.com/rgl/packer-provisioner-windows-update/releases "https://github.com/rgl/packer-provisioner-windows-update/releases")
* A copy of VMware tools - [https://my.vmware.com/web/vmware/details?productId=742&downloadGroup=VMTOOLS1032](https://my.vmware.com/web/vmware/details?productId=742&downloadGroup=VMTOOLS1032 "https://my.vmware.com/web/vmware/details?productId=742&downloadGroup=VMTOOLS1032")
* Your Windows ISO

Download, clone, and extract them all into a folder. I tend to prefer a single build folder with all of my components, the executables, scripts, and manifests. It allows easy portability into new environments. I tend to stage an iso folder and a VMware Tools folder within my build folder as well.

Next up, copy the `vsphere-variables.json.example` file, remove the example extension, and fill in the fields to match your environment. The variables contents are case sensitive. The username and password is not stored in the variables file, but passed through on the build execution for security.

Then upload your ISOs and VMware Tools to your datastore, I tend to use an ISO folder for containment.

### Review Settings & Configs

The Packer build process for Windows machines has three major components.

* The Packer `*.json` manifest file
* The Windows `Autounattend.xml` file
* Scripts in the scripts folder

I have split up the Packer manifests into a base and update components. This way if we have a need for a server on a specific patch level we can clone the base to do so and if there are issues it will fail faster.

Within the base manifests there are some relevant lines that might need to be changed to suit your naming and ISO/Tools location.

    "vm_name": "Template_Win2016_Std_64bit_Base",
    "iso_paths": [
    	"[{{user `vsphere_datastore`}}] ISO/SW_DVD9_WIN_SERVER_STD_CORE_2016_64BIT_ENGLISH_-4_DC_STD_MLF_X21-70526.ISO",
    	"[{{user `vsphere_datastore`}}] ISO/vmwareTools1032/vmtools/windows.iso"
    ],
    "floppy_img_path": "[{{user `vsphere_datastore`}}] ISO/vmwareTools1032/floppies/pvscsi-Windows8.flp"

The Windows `Autounattend.xml` file is somewhat standard, containing a number of quality of life changes I like for my servers. The major alteration are those for WinRM and a WinRM account. Packer uses WinRM heavily for configuring the windows environment and needs a local administrator account to do so.

Finally, there are a number of scripts to move the build process along and set some defaults.

### Build the Template

![](/uploads/buildBase.png=400x)