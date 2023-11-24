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
1. Install Cisco AnyConnect VPN client. You can use the following [link](https://drive.google.com/drive/folders/1mLl8bhqwmX3Okgn2Cl-WAQ09oenTv0bb?usp=drive_link) to request access to download the client for Mac, windows, or Linux. We will give you access immediately after your request.
2. Run Cisco AnyConnect client.
3. Contact Siddharth Gupta at +41787296208 (siddharth.gupta3@huawei.com) and ask for a temporary password so you use it in the next step. We already talked to Siddharth and he kindly agreed to make himself available at the reviewer's request.
4. Enter kuanywhere.ku.edu for the address, and hit "connect". 
<img src="/Cisco_VPN_Connect_Instructions/connect_init.png" width=200/>
5. Select Group as "DuoAuthentication"
Username: sgupta_sta
Password: hpca2024_AE
Second Password: [The temporary password that Siddharth Gupta Provides] 
<img src="/Cisco_VPN_Connect_Instructions/connect.png" width=200/>
- Alternatively: type "push" into the Second Password field and notify Siddharth Gupta
6. Now you are connected to the VPN and should be able to SSH to the servers.

Logging in to the servers can be performed over ssh once connected to the VPN:
```sh
ssh sgupta@castor.ittc.ku.edu
ssh sgupta@pollux.ittc.ku.edu
ssh sgupta@axdimm.ittc.ku.edu
```
* above commands provide access to the servers used throughout the subsequent steps

#### Nginx Workload Experiments
* Corresponds to figures 11 and 12 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

```sh
ssh sgupta@castor.ittc.ku.edu #login to workload generator
cd /home/n869p538/wrk_offloadenginesupport

./run_tls.sh # performs max_rps tls test from fig. 11
			 # prints results to stdout (Normalize to http configuration for RPS comparison in Fig. 11)
./run_tls_const.sh # performs system resource utilization tls test form fig.11
			 # prints results to stdout (Normalize to http configuration for RPS comparison in Fig. 11)

./run_gzip.sh # performs max_rps nginx gzip server test from fig.12
			  # prints results to stdout (Normalize to http configuration for RPS comparison in Fig. 11)
./run_gzip_const.sh # performs system resource utilization nginx gzip server test from fig.12
			  # prints results to stdout (Normalize to http configuration for RPS comparison in Fig. 12)
```

#### SmartDIMM Sensitivity Analysis
* Corresponds to figure 10 in [SmartDIMM:  In-Memory Acceleration of Upper Layer I/O Protocols Artifact](https://www.hpca-conf.org/2024)<br>

##### FPGA Programming (Control PC)
* To program the bit file onto the FPGA of the AXDIMM and activate the ILAs for result collection, follow these steps:

```
# Access pollux (Control PC) via SSH
ssh sgupta@pollux.ittc.ku.edu             

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
ssh sgupta@axdimm.ittc.ku.edu           

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
