# **Hyper-V notes:**
---

# **Concepts/Terms:**

### VHD/VHDX:
- **VHD/VHDX**: These are virtual hard disk formats used by Hyper-V to store the virtual machine's data. VHDX is the newer and more advanced format compared to VHD.
### .iso:
- **.iso**: An ISO file is a disk image file that contains the contents of an optical disc. It's pretty much always the way to install operating systems on virtual machines (or for that matter, on physical hosts).
### Differenced Disks:
- **Differenced Disks**: Differenced disks are child disks of a target VHDX. When you use a differenced disk, you can utilize the configuration of the parent VHDX while storing all changes to the differencing disk. These disks are useful when you need to maintain an immutable VHDX (unchangeable) for specific reasons, allowing one or multiple Hyper-V instances to use the disk's configuration without modifying it.
  - **Pros and Cons of Differencing Disks**:
    - **Pros**: Differencing disks provide efficient disk utilization by storing only the changes made to the VM, allowing for quick creation of multiple VMs while conserving storage space. They also allow for easy revert to the original state by deleting the differencing disk.
    - **Cons**: Managing multiple layers of differencing disks can become complex and impact performance. Regular maintenance and monitoring are crucial to ensure the integrity and performance of VMs using differencing disks.

# **Interacting with Hyper-V through PowerShell:**

1. **Get-VM**: `Get-VM -Name "VM1"` retrieves information about a virtual machine named "VM1".

2. **New-VM**: `New-VM -Name "NewVM" -MemoryStartupBytes 2GB` creates a new virtual machine named "NewVM" with 2GB of startup memory.

3. **Start-VM**: `Start-VM -Name "VM1"` starts the virtual machine named "VM1".

4. **Stop-VM**: `Stop-VM -Name "VM1"` stops the virtual machine named "VM1".

5. **Remove-VM**: `Remove-VM -Name "VM1"` deletes the virtual machine named "VM1".

6. **Set-VM**: `Set-VM -Name "VM1" -ProcessorCount 4` modifies the configuration of the virtual machine named "VM1" to have 4 processors.
  
7. **Get-VMNetworkAdapter**: `Get-VMNetworkAdapter -VMName "VM1"` retrieves information about the network adapters of the virtual machine named "VM1".

8. **New-VHD**: `New-VHD -Path "C:\VMs\Disk1.vhdx" -SizeBytes 100GB` creates a new virtual hard disk named "Disk1.vhdx" with a size of 100GB.

9. **Mount-VHD**: `Mount-VHD -Path "C:\VMs\Disk1.vhdx"` mounts the virtual hard disk located at "C:\VMs\Disk1.vhdx".
  
10. **Dismount-VHD**: `Dismount-VHD -Path "C:\VMs\Disk1.vhdx"` dismounts the virtual hard disk located at "C:\VMs\Disk1.vhdx".

11. **Resize-VHD**: `Resize-VHD -Path "C:\VMs\Disk1.vhdx" -SizeBytes 200GB` resizes the virtual hard disk located at "C:\VMs\Disk1.vhdx" to 200GB.

12. **Export-VM**: `Export-VM -Name "VM1" -Path "C:\Exports\VM1"` exports the virtual machine named "VM1" to the specified path.

13. **Import-VM**: `Import-VM -Path "C:\Exports\VM1"` imports a previously exported virtual machine located at "C:\Exports\VM1".

14. **Get-VMHost**: `Get-VMHost` retrieves information about the Hyper-V host.

15. **Enable-VMIntegrationService**: `Enable-VMIntegrationService -VMName "VM1" -Name "GuestServiceInterface"` enables the specified integration service for the virtual machine named "VM1".