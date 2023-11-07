## SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact
This repository contains scripts for SmartDIMM HPCA'24 artifact evaluation of the 
**SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact** paper by 
Neel Patel, Amin Mamandipoor, and Mohammad Alian

### Evaluation instructions ###
* start by executing `git submodule update --init --recursive` to fetch all submodules and dependencies

The scripts in this paper can be used to reproduce:
* Figure 10: SmartDIMM Scratchpad Utilization
* Figure 11: Encrypted Nginx Performance (Nginx Server for Encrypted Files) Measures RPS, CPU Utilization,
and Memory Bandwidth for a server producing TLS-encrypted HTTP responses driven by the wrk workload 
generator
* Figure 12: Compressed Nginx Performance (Nginx Server for Compressed Files) Measures RPS, CPU Utilization,
and Memory Bandwidth for a server producing gzip-compressed HTTP responses driven by the wrk workload 
generator

### Directory Structure
```
|--- Near-Memory-Sensitivity-Analysis: SmartDIMM sensitivity analysis examining the resource utilization for different server loads
|--- wrk_offloadenginesupport: workload generation scripts based on the `wrk` http request generation tool
  |--- async_nginx_build: nginx server configuration files and builds for baseline http, https, and accelerated https using QAT and kTLS
```

### SmartDIMM Sensitivity Analysis
* Corresponds to figure 10 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

#### Artifact Evaluation Instructions:
To ease reproducibility for our artifact evaluators we have provided on-premise access to the AxDIMM testbench used to generate the original SmartDIMM sensitivity analyses from the HPCA 2024 paper `SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`

#### Logging in to the Workload Generator (Castor)
*

#### Arming the ILAs:

#### Logging in to AxDIMM (SmartDIMM Testbench):
*

#### Starting Workload Generation:
* Initiates `ComputeCopy` Threads performing memcpy's to/from the AxDIMM's registered address space
```
```

#### Generating Sensitivity Analysis Figures


### Nginx Workload Experiments
* Corresponds to figures 11 and 12 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

#### Setting up Cloudlab Instances:
To ease reproduction we have created a cloudlab environment and setup closely matching the server configuration used in the HPCA 2024 paper `SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`

Following the instructions below will provision a cloudlab instance in which the SPEC 2017 and (de)compression workloads
from the paper will be executed. For more information, refer to [`SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`](https://www.HPCA 56.org/)

* allocate a cloudlab instance using the genilib script provided in this repo
	* Create a cloudlab account if needed
	* Navigate to `Experiments`, then `Create Experiment Profile`, and upload `nginx_workload.profile`


* As resource availability of nodes on the `Clemson` cluster equipped with Mellanox Bluefield 2 SmartNICs cannot be guaranteed ***we provide separate instructions for our artifact evaluators below***.

#### Artifact Evaluation Instructions:
To ease reproducibility for our artifact evaluators we have provided on-premise access to the servers used to generate the original nginx workload results from the HPCA 2024 paper `SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`
* temporary access to the AxDIMM FPGA Testbench used for conducting sensitivity analysis has been provided to the Artifact Evaluators
* to expedite result generation, pre-built versions of the nginx builds and workload generators have been provided on the
workload generation and DUT servers
* please reach out to the authors if there are any further questions regarding accessing our on-premise hosts