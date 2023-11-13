## SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact
This repository contains scripts for SmartDIMM HPCA'24 artifact evaluation of the 
**SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact** paper by 
Neel Patel, Amin Mamandipoor, and Mohammad Alian

***HPCA 2024 Artifact Evaluators*** should skip to [Artifact Evaluation instructions](#artifact-evaluation-instructions) below

### Evaluation instructions
* start by executing `git submodule update --init --recursive` to fetch all submodules and dependencies
The scripts in this repository can be used to reproduce:
* Figure 10: SmartDIMM Scratchpad Utilization
* Figure 11: Encrypted Nginx Performance (Nginx Server for Encrypted Files) Measures RPS, CPU Utilization,
and Memory Bandwidth for a server producing TLS-encrypted HTTP responses driven by the wrk workload 
generator
* Figure 12: Compressed Nginx Performance (Nginx Server for Compressed Files) Measures RPS, CPU Utilization,
and Memory Bandwidth for a server producing gzip-compressed HTTP responses driven by the wrk workload 
generator

* As resource availability of nodes on the `Clemson` cluster equipped with Mellanox Bluefield 2 SmartNICs cannot be guaranteed ***we provide separate [instructions for our artifact evaluators below](#artifact-evaluation-instructions)***.

### Directory Structure
```
|--- Near-Memory-Sensitivity-Analysis: SmartDIMM sensitivity analysis examining the resource utilization for different server loads
|--- wrk_offloadenginesupport: workload generation scripts based on the `wrk` http request generation tool
  |--- async_nginx_build: nginx server configuration files and builds for baseline http, https, and accelerated https using QAT and kTLS
```

#### Setting up Cloudlab Instances:
To ease reproduction we have created a cloudlab environment and setup closely matching the server configuration used in the HPCA 2024 paper `SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`

Following the instructions below will provision a cloudlab instance in which the SPEC 2017 and (de)compression workloads
from the paper will be executed. For more information, refer to [`SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`](https://www.HPCA 56.org/)

* allocate a cloudlab instance using the genilib script provided in this repo
	* Create a cloudlab account if needed
	* Navigate to `Experiments`, then `Create Experiment Profile`, and upload `nginx_workload.profile`

#### Compiling DUT servers
* on DUT node
```sh
git clone git@github.com:architecture-research-group/SmartDIMM_ArtifactEvaluation.git
git submodule update --init --recursive
cd wrk_offloadenginesupport/async_nginx_build
make all
```

#### Compiling Workload Generators
* on Workload Generator node
```sh
git clone git@github.com:architecture-research-group/SmartDIMM_ArtifactEvaluation.git
git submodule update --init --recursive
cd wrk_offloadenginesupport
make all
```

### Artifact Evaluation Instructions:
To ease reproducibility for our artifact evaluators we have provided on-premise access to the servers used to generate the original nginx workload results from the HPCA 2024 paper `SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact`
* temporary access to the AxDIMM FPGA Testbench used for conducting sensitivity analysis has been provided to the Artifact Evaluators
* to expedite result generation, pre-built versions of the nginx builds and workload generators have been provided on the
workload generation and DUT servers
* please reach out to the authors if there are any further questions regarding accessing our on-premise hosts

#### SmartDIMM Sensitivity Analysis
* Corresponds to figure 10 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

##### FPGA Programming (Control PC)
* To program the bit file onto the FPGA of the AXDIMM and activate the ILAs for result collection, follow these steps:

```
# Access pollux (Control PC) via SSH
ssh UserName@pollux             

# Navigate to the programFPGA directory
cd programFPGA

# Execute the run.sh script with specified FolderName and FileName
./run.sh FolderName FileName 
```

##### Starting Workload Generation:
* Initiates `ComputeCopy` Threads performing memcpy's to/from the AxDIMM's registered address space
```
# Access axdimm via SSH
ssh UserName@axdimm             

# Navigate to the ? directory
cd ?

# Execute the test.sh script 
./test.sh  
```

##### Generating Sensitivity Analysis Figures
* Lastly, parse the collected CSV files and generate plots using the following commands:
```
# Navigate to the ? directory
cd ?

python3 fig10Plot.py  
```



#### Nginx Workload Experiments
* Corresponds to figures 11 and 12 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

###### Logging in to the Workload Generator (Castor)
* After logging in, follow instructions in the README.md of `wrk_offloadenginesupport`

###### Logging in to the DUT (Pollux)
* After logging in, follow instructions in the README.md of `wrk_offloadenginesupport/async_nginx_build`
