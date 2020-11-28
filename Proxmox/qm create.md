https://cloud.centos.org/centos/8/x86_64/images/

# Create the instance
	qm create 8000 -name centos-cloudinit -memory 1024 -net0 virtio,bridge=vmbr0 -cores 1 -sockets 1

# Import the OpenStack disk image to Proxmox storage
	qm importdisk 8000 CentOS-8-ec2-8.1.1911-20200113.3.x86_64.qcow2 local-lvm

# Attach the disk to the virtual machine
	qm set 8000 -scsihw virtio-scsi-pci -virtio0 local-lvm:vm-8000-disk-0

# Add a serial output
	qm set 8000 -serial0 socket

# Set the bootdisk to the imported Openstack disk
	qm set 8000 -boot c -bootdisk virtio0

# Enable the Qemu agent
	qm set 8000 -agent 1

# Allow hotplugging of network, USB and disks
	qm set 8000 -hotplug disk,network,usb

# Add a single vCPU (for now)
	qm set 8000 -vcpus 1

# Add a video output
	qm set 8000 -vga qxl

# Set a second hard drive, using the inbuilt cloudinit drive
	qm set 8000 -ide2 local-lvm:cloudinit

# Resize the primary boot disk (otherwise it will be around 2G by default)
# This step adds another 8G of disk space, but change this as you need to
	qm resize 8000 virtio0 +15G

# Convert the VM to the template
	qm template 8000


