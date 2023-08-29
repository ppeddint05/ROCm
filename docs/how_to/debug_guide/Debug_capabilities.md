## **Debug Capabilities**

**Commonly used debug commands**

-   The following commands or script can be used to gather the DMESG for troubleshooting:

        - `dmesg -T`
        - `journalctl`
        - [ROCm Tech Support](https://github.com/amddcgpuce/rocmtechsupport/)

-   Enable journal service to persist logs across reboots. Once enabled, it will start on reboot:
        
	```
	sudo systemctl restart systemd-journald.service
        sudo journalctl -b [For current boot logs]
        sudo journalctl -b -1 [Minus one for previous boot logs]
        sudo journalctl -b --2
	```	

-    How to save dmesg in a hostname plus date timestamp:
	
        ```
	dmesg > dmesg-`hostname``date +%m_%d_%H_%M_%S`.log
	```
				
-    How to capture previous boot logs and save them:

        ```
	sudo journalctl -b -1 > prev_bootlogs-`hostname``date +%m_%d_%H_%M_%S`.log
	```									     										 

-    How to restart the journal service:
	
        ```
        sudo systemctl restart systemd-journald.service
        ```

-    How to look for a specific error from the DMESG logs, here is an example of looking for a `deferred` error in dmseg log:

        ```
	dmesg > dmesg.tmp; grep -i "deferred|" dmesg.tmp | awk '{print $5 " " $7}' | uniq
	grep -i "deferred|" $1 | awk '{print $5 " " $7}' | uniq
        ```

**ROCm related debug options**

Many or most ROCm components have additional debug messaging capabilities that can be turned on if necessary to aid in debugging and troubleshooting effort. While each component's debug capability is highly specific in terms of syntax, log level as well as ROCm layer being analyzed, below are commonly used examples. 

For a full comprehensive list of debug log options, refer to Appendix A.

Most of ROCm libraries have additional level of log output capability which are enabled by setting environmental variable in the usual way before launching executable:

        ```
        <ENV_VAR_NAME>=<ENV_VAR_NAME_VALUE> <EXECUTABLE>
        ```

Below are examples:
1. Enabling HIP layer log to 4 and running workload.
2. INFO level debugging logs by setting environment variable when running RCCL based workload:

**Example 1:**

[Vectoradd](https://github.com/ROCm-Developer-Tools/HIP-Examples/tree/master/vectorAdd) example code using `AMD_LOG_LEVEL` will output additional log message in HIP/ROCclr layer.
    
        ```
        AMD_LOG_LEVEL=4 ./vectoradd_hip.exe
        ```
    
**Example 2:**

[HelloRccl test](https://github.com/ROCmSoftwarePlatform/rccl/tree/develop/tools/HelloRccl) performs an allreduce on single GPU node. To enable INFO log level during execution, user can run below:

        ```
	NCCL_DEBUG=INFO ./HelloRccl 1
        ```

Since `HelloRccl` uses RCCL and is written in HIP API, user can use both `AMD_LOG_LEVEL` and `NCCL_DEBUG` environment variables as follows:

        ```
	AMD_LOG_LEVEL=4 NCCL_DEBUG=INFO ./HelloRccl 1
	```
