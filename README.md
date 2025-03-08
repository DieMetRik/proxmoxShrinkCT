# Proxmox LXC Shrink Root Disk Script

## Overview
This script provides an automated way to shrink the root disk of a Proxmox LXC container. It ensures the process is simple, efficient, and minimizes manual steps. **Use at your own risk**, and always ensure you have proper backups before resizing any containers.
This was created for containers only. I'm unsure if it would work for VM.

---

## Features
- Lists all containers (`pct list`) for easy selection.
- Automatically stops the selected container (if running).
- Identifies the logical volume path of the container.
- Activates the logical volume, resizes it, and modifies the configuration file.
- Restarts the container after successful resizing.
- Includes user-friendly prompts and validation.

---

## Prerequisites
- **Proxmox Virtual environment** with LXC containers.
- Root access to the Proxmox node.
- Backup of your containers (strongly recommended, preferably using rule 3-2-1).

---

## How to Use
1. Clone this repository (you must have git installed, or you can download and transfer manually to your server):
```
git clone https://github.com/mchiappinam/proxmoxShrinkCT.git
cd proxmoxShrinkCT
```

2. Make the script executable:
```
chmod +x shrinkContainer.sh
```

3. Run the script:
```
./shrinkContainer.sh
```

4. Follow the prompts:
   - Select the **VMID** of the container to resize.
   - Enter the desired new size (e.g., `10G` for 10 GiB).

---

## Notes
- The script assumes the default LVM storage layout (`/dev/pve`). Modify it if your setup differs.
- Tested using PVE 8.2.7
- To fix the warning below, I had to backup and restore the LXC. If you know a better way to fix it, please commit the change or let me know.
	- "WARNING: Thin volume pve/vm-100-disk-0 maps X while the size is only X."

---

## Example Output
```
user@proxmox:~# ./shrinkContainer.sh
     #############################################
     #                                           #
     #      Script powered by Chiappina.com      #
     #                                           #
     #############################################

     Disclaimer: Use this script at your own risk!     
Always ensure you have proper backups before proceeding.

Fetching container list...
VMID       Status     Lock         Name                
100        running                 on                  
101        running                 oshdaw              
102        running                 uhz                 
103        running                 lhbb                
104        running                 iqzgbtpo            
105        running                 zgqop               
110        running                 mbihdal             

Enter the VMID of the container you want to resize: 110
Enter the new size (e.g., 5G): 5G
You have selected VMID: 110 and new size: 5G
Are you sure you want to continue? (y/n): y
Stopping container 110...
Container 110 stopped successfully.
Finding logical volume path for VMID 110...
  WARNING: LV pve/vm-110-disk-0 maps <31.08 GiB while the size is only 10.00 GiB.
  WARNING: LV pve/vm-100-disk-0 maps 137.02 GiB while the size is only 32.00 GiB.
LV Path: /dev/pve/vm-110-disk-0
Activating logical volume /dev/pve/vm-110-disk-0...
Reducing logical volume and filesystem to 5G...
fsck from util-linux 2.38.1
/dev/mapper/pve-vm--110--disk--0: 37619/655360 files (0.6% non-contiguous), 819975/2621440 blocks
resize2fs 1.47.0 (5-Feb-2023)
Resizing the filesystem on /dev/mapper/pve-vm--110--disk--0 to 1310720 (4k) blocks.
The filesystem on /dev/mapper/pve-vm--110--disk--0 is now 1310720 (4k) blocks long.

  Size of logical volume pve/vm-110-disk-0 changed from 10.00 GiB (2560 extents) to 5.00 GiB (1280 extents).
  Logical volume pve/vm-110-disk-0 successfully resized.
Deactivating logical volume /dev/pve/vm-110-disk-0...
Updating container configuration file /etc/pve/lxc/110.conf...
Starting container 110...
  WARNING: Thin volume pve/vm-100-disk-0 maps 147126026240 while the size is only 34359738368.
  WARNING: Thin volume pve/vm-100-disk-0 maps 147126026240 while the size is only 34359738368.
Container 110 resized and started successfully!
user@proxmox:~# 
```

---

## Author
**[Chiappina.com](https://Chiappina.com)**
