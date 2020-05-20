+++
date = "2019-10-23T18:00:00+00:00"
tags = []
title = "Easy Maintenance Mode vMotion Migration of vGPU Backed VMs"

+++
I ran into this issue earlier and didn't find it well documented.

When you put a vGPU host into maintenance mode, the vGPU VMs don't automatically migrate off of the host. Annoying.

It turns out that vMotion uses DRS to evacuate hosts and DRS doesn't automatically act on recommendations for vGPU backed VMs. But we can manually act on the recommendations. Push the apply button in DRS and away it goes.

For detailed info on how to easily maintenance mode your vGPU hosts:

First, you need to make sure that your vCenter is 6.7 U1 or greater for the vMotion of vGPU VM feature to be available. Then, to enable vGPU vMotion, you just need to update the following vCenter Server Advanced Setting **vgpu.hotmigrate.enabled** to _true_ using the vSphere UI. The change will go into effect immediately and you will now be able to vMotion a VM configured with vGPU.

![](/uploads/vgpu-hotmigrate.png)

Next, double check it is working by migrating a VM. If it works, you are ready to maintain, if  it is complaining about GPU resources you might have too many profiles, not enough cards, or are not running in consolidate mode.

![](/uploads/vgpu-consolidate.png)

With vGPU vMotion working, time to drain the host.  Put it in maintenance mode, head over to your DRS Recommendations, look at all the happy jobs waiting for you.

![](/uploads/vgpu-run-drs.png)

Push the magic button, watch your vGPU VMs fly around, and allow the host to enter maintenance mode.

![](/uploads/vgpu-apply.png)

Repeat as the patching cycle requires.