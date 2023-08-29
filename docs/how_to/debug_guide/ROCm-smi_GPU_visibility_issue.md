**ROCm-smi GPU visibility issue**

**Error signature:**

```
   $rocm-smi --showdriverversion
   ========================ROCm System Management Interface========================   
   WARNING: No AMD GPUs specified   
    
   $rocm-smi --showall
   ========================ROCm System Management Interface========================
   WARNING: No AMD GPUs specified
   =========================================================================   
   
   $rocm-smi --alldevices   
   ========================ROCm System Management Interface========================  
   WARNING: No AMD GPUs specified   
   =========================================================================   
```


**Steps to debug:**



[PlaceHolder for flowchart]
![A diagram of a Debug flowchart for GPU visibility issue ](./images/media/debug_flowchart.png){width="4.558333333333334in" height="7.811170166229221in"}

**Figure 1: Debug Flowchart for GPU visibility issue**

Figure 1 shows the debug flowchart for the rocm-smi GPU visibility issue whose error signature is as shown above. Following steps can be used to debug such an error signature:

-   Check the device files and see if the GPUs are listed in the /dev/dri directory path as below:
    ```
    $ ls -l /dev/dri
    total 0
    crw-rw-rw- 1 root root 226, 0 May 23 08:43 card0
    ```

    If the the GPU cards are visible as `card*`, then check if `lspci` outputs the GPU cards. To further debug the `lspci` not displaying the GPU cards, refer to [common hardware Debug methods](./Common_hardware_debug_methods.md) section.

-   Check if AMDGPU driver is loaded,

    -   If the AMDGPU driver is loaded successfully, the `lspci` output should have the AMDGPU as the `kernel driver in use:` on every GPU on the system as shown in the below snippet:
    
        ```
        $ sudo lspci -k -s b3:00.0
        b3:00.0 Display controller: Advanced Micro Devices, Inc. [AMD/ATI] Aldebaran (rev 02)
        Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Aldebaran
        Kernel driver in use: amdgpu
        Kernel modules: amdgpu
        ```

    -   If not reload the driver using `modprobe -r amdgpu`. Check the `dmseg` logs to see if any errors occurred, below is an example of driver load fail errors:

        ```
        $ dmesg -T |grep amdgpu  
        [Fri May 22 15:26:41 2020] [drm] amdgpu kernel modesetting enabled.  
        [Fri May 22 15:26:41 2020] [drm] amdgpu version: 5.4.8  
        [Fri May 22 15:26:41 2020] amdgpu 0000:43:00.0: enabling device (0000 -> 0003)  
        [Fri May 22 15:26:41 2020] amdgpu 0000:43:00.0: Failed to load gpu_info firmware "amdgpu/arcturus_gpu_info.bin"  
        [Fri May 22 15:26:41 2020] amdgpu 0000:43:00.0: Fatal error during GPU init  
        [Fri May 22 15:26:41 2020] [drm] amdgpu: finishing device.  
        [Fri May 22 15:26:41 2020] amdgpu: probe of 0000:43:00.0 failed with error -2  
        [Fri May 22 15:26:41 2020] amdgpu 0000:03:00.0: enabling device (0000 -> 0003)  
        [Fri May 22 15:26:41 2020] amdgpu 0000:03:00.0: Failed to load gpu_info firmware "amdgpu/arcturus_gpu_info.bin"  
        [Fri May 22 15:26:41 2020] amdgpu 0000:03:00.0: Fatal error during GPU init  
        [Fri May 22 15:26:41 2020] [drm] amdgpu: finishing device.  
        [Fri May 22 15:26:41 2020] amdgpu: probe of 0000:03:00.0 failed with error -2  
        [Fri May 22 15:26:41 2020] amdgpu 0000:23:00.0: enabling device (0000 -> 0003)  
        [Fri May 22 15:26:41 2020] amdgpu 0000:23:00.0: Failed to load gpu_info firmware "amdgpu/arcturus_gpu_info.bin"  
        [Fri May 22 15:26:41 2020] amdgpu 0000:23:00.0: Fatal error during GPU init  
        [Fri May 22 15:26:41 2020] [drm] amdgpu: finishing device.  
        [Fri May 22 15:26:41 2020] amdgpu: probe of 0000:23:00.0 failed with error -2  
        [Fri May 22 15:26:41 2020] amdgpu 0000:26:00.0: enabling device (0000 -> 0003)  
        [Fri May 22 15:26:41 2020] amdgpu 0000:26:00.0: Failed to load gpu_info firmware "amdgpu/.arcturus_gpu_info.bin"  
        [Fri May 22 15:26:41 2020] amdgpu 0000:26:00.0: Fatal error during GPU init  
        [Fri May 22 15:26:41 2020] [drm] amdgpu: finishing device.  
        [Fri May 22 15:26:41 2020] amdgpu: probe of 0000:26:00.0 failed with error -2  
        ```

    -   If there are any such errors, you see in the dmesg log, refer [AMDGPU driver loading errors](./AMDGPU_driver_loading_errors.md) for further help.

    -   If that doesn't resolve, file an AMD JIRA ticket attaching the logs from running the [rocmtechsupport script](https://github.com/amddcgpuce/rocmtechsupport/)

-   If the driver is not installed, please refer [AMDGPU driver installation steps](https://rocm.docs.amd.com/en/latest/deploy/linux/installer/install.html) for further install instructions.
    

-   Make sure that the AMDGPU driver is installed for the correct booted kernel version.
    
    Though the rebuild of the DKMS modules is usually seamless during a kernel upgrade, it may still happen that the rebuild of the AMDGPU driver fails. For example, if you see the dkms status output below, it shows that the AMDGPU driver is not installed for the booted kernel version **5.15.0.58.**
    ```
    ===== Section: dkms status ===============
    amdgpu, 5.13.5-1343156, 5.11.0-40-generic, x86_64: installed
    amdgpu, 5.13.5-1343156, 5.8.0-50-generic, x86_64: installed
    ```

    Reinstall the AMDGPU driver to match the booted kernel version using the instructions provided [here](https://rocm.docs.amd.com/en/latest/deploy/linux/installer/install.html). In the case above, the system is running an upstream AMDGPU driver which doesn't work for ROCm.

-   If there is one or fewer GPUs that are not visible on the `rocm-smi`, check the vbios version using the following command:
    
    ```
    $ rocm-smi --showhw
    
    ======================= ROCm System Management Interface =======================
    ============================ Concise Hardware Info =============================
    GPU  DID   GFX RAS  SDMA RAS  UMC RAS  VBIOS           BUS
    0    740f  ENABLED  ENABLED   ENABLED  113-D67301-059  0000:1D:00.0
    1    740f  ENABLED  ENABLED   ENABLED  113-D67301-059  0000:8A:00.0
    ================================================================================
    ============================= End of ROCm SMI Log ==============================
    ```
    Make sure the vbios versions on each of the GPUs match the recommended version as provided by the AMD team and use `amdfwflash` [user guide](https://drivers.amd.com/relnotes/amdfwflash_userguide_58083_1.21.pdf) tool to update the firmware correctly.
