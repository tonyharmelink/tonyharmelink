+++
date = 2020-05-20T19:06:00Z
tags = []
title = "Complete EUC Overhaul with 3D CAD VDI and Workspace ONE Part 1 - Investigating Prior State and Planning"

+++
The chance rarely comes around to completely rethink and overhaul the end user experience of an organization. Even through bigger upgrades, switching from Windows 7 to Windows 10 for instance, we often try to replicate what the past experience was.

We didn't do that that. We changed almost everything.

* Lenovo & HP -> Dell
* Windows 7 -> Windows 10
* Novell Groupwise -> Microsoft Outlook 365
* Novell eDirectory -> Windows Active Directory
* Novell File Server -> Windows DFS
* Novell ZenWorks -> VMware Workspace ONE UEM
* VMware Horizon View w/ NVIDIA GRID
* VMware Workspace ONE Access
* Cisco CUCM -> Microsoft Teams w/ SIP SBC

### Prior State

When I started at my current employer, the other two team members had spent the past couple years refreshing the backend infrastructure. Implementing a new network stack, Cisco ISE for more security, and a new compute cluster on VMware Cloud Foundation. Groupwise was converted to Office 365 and AD had been initially setup. The rest of the EUC space had been stagnant, waiting for me to come in and refresh it. Windows 7 desktops and laptops with software deployed manually or with Zenworks. Eek.

When I was brought on I came in knowing I had a lot of work to do, an exciting prospect for one who loves projects. I was sold on the desired state pitch, 3D CAD in VDI for the designers, Mobile Device Management rollout and Windows 10 Modern Management with Workspace ONE UEM, Identity Federation and Zero Trust implementation with Workspace ONE Access. Pretty much the whole EUC stack that VMware offers.

### My Prior Knowledge

My role previous to this one was also focused on VDI, but with completely different goals, connections, and workloads. It was 20+ distributed branches in the midwest, many with sub 5Mbps connections, accessing a central VDI setup with no hardware on site other than networking and PCOIP zero clients. Workload was standard office worker, outlook, spreadsheet looking ERP system, office apps, perfect use for PCOIP. It was super efficient, to the point where we had one site on 3Mbps from bonded T1s, running 20 users, and the one day some chucklehead decided to stream music in his VDI it used half the bandwidth for the entire branch. But now I was entering into something completely different, 3D CAD, vGPUs, high motion, minimal latency required, motion to photon as low as possible.

### Big First Step: Engage the Business

One of the first things that needs to be started with a major overhaul is engaging the business. A lot of buy-in is needed to pull off dramatic user facing changes. It can be hard for some users to adapt as changes keep coming in rapid succession. The upside is that radical change provides the opportunity to re-evaluate how work is done. For this project, a file server migration turned into a file server re-architecture. The decision was made to hold off on cloud files for security and legacy software reasons, but the migration was taken advantage of to archive the 25 year old file server folder structure and move work processes into a new file storage structure that better reflects the current workflow.

### Desired State/Goals

##### Virtualize the ERP System

There has long been a desire for external access, and from the IT side we wanted it secured by geofenced zero trust since it carries financial data. It's an old, weird ERP system that 'doesn't support remote access due to latency.' So we planned to put it in VDI and also be served via RDSH to non VDI users.

##### Virtualize the CAD Designers

This was probably the biggest and most exciting one for me, shooting for the stars in terms of complexity. And to add to it I made the choice to go non-persistent with Instant Clones, AppVolumes, and DEM. Later on FSLogix was thrown into the mix to make OneDrive work and speed up profiles.

##### Provide VDI to All Users

Continuing on the path for remote access. VDI provides the best experience, so far beyond VPN from corporate devices. And it is easy to complete when covering the VDI CAD pool, it's just fewer layers after all. With also deploying Workspace ONE Access it gives us confidence that we are well protected from the wild west that is home users devices connecting to a corporate network.

##### Mobile Device Management and BYOD enablement

There was the desire for the company to stop being a phone company, cut down on corporate owned phones and allow secure access to company resources on personal devices. It was time to learn Workspace ONE UEM.

##### Windows 10 Modern Management

Again, time to learn Workspace ONE UEM. Airwatch has always been considered one of the best MDM/MAM platforms. The Windows 10 Modern Management parts of it are much more complete than they were when I started using it and getting close to being the best available on the market, but is somewhat held back by legacy design decisions from a mobile centric platform.

##### Identity Federation with Workspace ONE Access

We decided to go all in on Workspace ONE Access, to the point we have federated our Office 365 deployment through Workspace ONE Access. The Intelligent Hub provided by Workspace One Access in conjunction with Workspace ONE UEM provides our users a single interface to learn, across Windows, Web, and Mobile to access their corporate resources, be it apps to install, websites with SAML logins, or Horizon VDI resources.

##### Modernize Communication and Collaboration with Microsoft Teams

We added this one late into the game. The introduction, training on usage, and cutting phones over from Cisco CUCM to Teams had been planned for a slow roll over 1-2 years.  then COVID-19 hit and we pushed hard to get it live and people acclimated. It is a fantastic platform for collaboration, especially when everyone is working remote.

##### Dell Endpoints & Servers

I have always had a great experience with Dell hardware and pushed for it to become the standard for a few reasons. The support is awesome, some of the best I have experienced, especially with ProSupport Plus. Then there are tight integrations with Workspace One UEM to make my life easier. I can deploy a profile to manage driver and BIOS updates through Dell Command: Update, set it and forget it. On the server side, PowerEdge ReadyNodes for VCF. Again tighter integrations with VMware VCF. We also chose them over vxRail, mostly because of our smaller size and we didn't feel the need for a management layer on our management layer.

### Coming up next:

* Using Systrak to gather App deployment, usage, and performance metrics to classify users
* Using GPUProfiler to gather GPU memory usage and GPU core usage on CAD Users
* Making Spreadsheet to plan out hardware spec
* Choosing Hardware to meet the spec
* Order up hardware