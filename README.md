# Remote-Gaming-and-Video-editing-with-Proxmox-GPU-passthrough-in-a-Windows-10-VM

Are you a PC enthusiast want to play around virtualization and have you just recently upgraded to a 3000 series RTX card and don't know what to do with your 2000 series well I have a project for you.


## I am going to assume you have a Proxmox Host with an extra GPU setup.

Once you pass the GPU through to a VM the host will not have access to the GPU anymore.  This is why I recomend adding a second GPU to your host.  Also the GPU cannot be shared across guests.  

## Changes to Proxmox Server
ssh into your Proxmox Server and edit Grub
```bash
ssh root@yourproxmoxipaddress
nano /etc/default/grub
```
Update the "GRUB_CMDLIN_LINUX_DEFAULT" line.  Bye default it will be set to quiet.  
![Alt](https://gist.githubusercontent.com/kstevenson722/99e78a337b6326a12b6aa098349b489e/raw/1ae97e9a83209e07a0920464e6c2112869ef4c07/z-grub-before-update.PNG "GRUB Line to update")

### Intel CPU
For Intel CPUs add "intel_iommu=on", for example:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```
Save changes and update grub:
```bash
update-grub
```
Reboot Proxmox Host

### Edit Modules
add to /etc/modules
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```
On your windows 10 vm you will want to make sure remote destop is turned on.  You may loose VNC access after you pass the GPU though.

### Edit virtual machine's config file
cpu: host,hidden=1,flags=+pcid

from proxmox add pci device.

### Block drivers from Host machine
edit /etc/modprobe.d/pve-blacklist.conf

block these drivers from being loaded
```
blacklist nvidiafb
blacklist nvidia
blacklist radeon
blacklist nouveau
```
