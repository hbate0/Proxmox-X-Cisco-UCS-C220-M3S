# Proxmox on Cisco UCS C220 M3 with Cisco UCS RAID SAS 2008M-8i

After struggling to find help online releasing my steps in this setup.

# Installation Guide
1. Make sure disks in server are Unconfigured. Do this by entering WebBIOS (Ctrl+H) on boot, goto Configuration Wizard tab and then click Clear Configuration.

2. Install Proxmox VE ISO image, download the ISO image from here: https://www.proxmox.com/en/downloads/category/iso-images-pve

3. Flash the ISO image to a bootable USB using BalenaEtcher: https://www.balena.io/etcher/

4. Plug the bootable USB into the server and boot the server, select the boot option menu by selecting F6 when prompted or whatever is specified on your system. Once in boot menu select the bootable USB to install Proxmox.

5. On install select the FlexiFlash card or disk "HV", this will be changed later. Continue with your Proxmox install it is quite straightforward.

6. Once you can access the system goto the Shell for proxmox and as root and follow this guide to install MegaCLI https://www.mybluelinux.com/debian-linux-and-lsi-megaraid-sas/

7. Once MegaCLI is installed it is time to do a JBOD configuration on the server so we can get passthrough for Proxmox.

8. Identify the enclosure with the command: 
```
megacli -EncInfo -a0
```
9. The following instructions come from here: https://www.broadcom.com/support/knowledgebase/1211161498596/megacli-cheat-sheet--live-examples
   
   To enable JBOD configuration follow these steps. NOTE: These steps cannot be undone.
   ```
   megacli -PDMakeJBOD -PhysDrv[E0:S0,E1:S1,...] -aN|-a0,1,2|-aALL
   ```
   You need to run encinfo (see above identify enclosure) and do not type E, only 252:4
   Where 252 is Enclosure and 4 is Slot
   Live example:
   ```
   megacli -pdmakejbod -physdrv [252:4] –a0
   ```
10. Once the configuration is done reboot.

11. Depending if you want to reinstall Proxmox to a bigger disk you can configure the disks with ZFS now.

# Re-Installing on a JBOD Disk

1. If you want to install Proxmox on a single disk after completing the config above plug in the Proxmox bootable USB and reinstall on one of the disks of your choice.

2. Once in Proxmox configure ZFS on remaining drives.

3. Wipe the HV Flexi Flash SD card once done reinstall and reboot. In boot menu change the new disk to the disk you reinstalled on.
