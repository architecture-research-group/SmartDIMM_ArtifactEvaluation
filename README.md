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
workload generation and DUT servers (castor and pollux)

Acquiring Credentials:
* please ***reach out to the authors over the `hpca2024ae hotcrp` site  to retrieve VPN access and credentials*** to access the aforementioned servers (username: sgupta pass: ***provided upon request*** )

Connecting to the VPN:
* the [Cisco AnyConnect VPN client](https://www.cisco.com/c/en/us/support/security/anyconnect-secure-mobility-client-v4-x/model.html#~tab-downloads) is used to gain internal access to the network on which the servers can be accessed
(1) Open the AnyConnect client once installation is complete.
(2) Enter `kuanywhere.ku.edu` for the address.
(3) Enter the Online ID and Password (acquired from HPCA2024 AE Chair Siddharth Gupta or the authors), leaving your group as default. 
In the case that the account requires MFA (Multi-Factor Authentication), follow the MFA-specific step below
(MFA) Install the [Duo Mobile authentication app](https://play.google.com/store/apps/details?id=com.duosecurity.duomobile&hl=en_US&gl=US&pli=1) and follow the [Duo mobile authentication first-time-setup instructions](https://kuit.service-now.com/kb?sys_kb_id=9d018174db43d744959e9434db961990&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=b771cf641ba235104ccf337e034bcb3b)
* Repeat Steps (2) and (3) above, but select "DuoAuthentication" from the Group dropdown in the Cisco Mobility Client and authenticate using one of the available authentication options under [Step 3](https://kuit.service-now.com/kb?id=kb_article_view&sysparm_article=KB0013837)
* please reach out to the authors if there are any further questions regarding accessing our on-premise hosts

Logging in to the servers can be performed over ssh one connected to the VPN:
```sh
ssh sgupta@castor.ittc.ku.edu
ssh sgupta@pollux.ittc.ku.edu
ssh sgupta@axdimm.ittc.ku.edu
```
* above commands provide access to the servers used throughout the subsequent steps

#### Nginx Workload Experiments
* Corresponds to figures 11 and 12 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

###### Logging in to the Workload Generator (Castor)
* `ssh sgupta@castor.ittc.ku.edu`
* Credentials provided via hpca2024 AE site
* After logging in, follow ***Artifact Evaluation*** instructions in the README.md of `/home/n869p538/wrk_offloadenginesupport`

#### SmartDIMM Sensitivity Analysis
* Corresponds to figure 10 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

##### FPGA Programming (Control PC)
* To program the bit file onto the FPGA of the AXDIMM and activate the ILAs for result collection, follow these steps:

```
# Access pollux (Control PC) via SSH
ssh sgupta@pollux             

# Navigate to the sensitivityAnalysis directory
cd /home/sgupta/sensitivityAnalysis/vivadoScript

# Execute the run.sh script with specified FolderName and FileName
source /tools/xilinx/Vivado/2019.2/settings64.sh
./run.sh FolderName FileName 
```

##### Starting Workload Generation:
* Initiates `ComputeCopy` Threads performing memcpy's to/from the AxDIMM's registered address space
```
# Access axdimm via SSH
ssh sgupta@axdimm             

# Navigate to the test directory
cd /home/sgupta/axdimm_scripts/antagonist_testing

# Execute the antagonist.sh alongside test.sh script 
./antagonist.sh & ./test.sh
```

##### Generating Sensitivity Analysis Figures
* Lastly, parse the collected CSV files and generate plots using the following commands:
```
# Navigate to the `FolderName` directory specified in 
cd /home/sgupta/sensitivityAnalysis

python3 emulation.py -csv PathToCSVFile-0 PathToCSVFile-1 PathToCSVFile-3  
```
