# AIHUB – XMUM AIT GPU Server 

## Rules and Tutorial on Remote Login, Module Loading, and Job Submission with Slurm



### Purpose of Use

This server is dedicated to training AI models and computational tasks related to your coursework or research in AI. Security testing, penetration testing, or unrelated tasks are strictly prohibited. Basic Linux skills are expected. 



### Server Usage Rules for Users

Authorized Users Only: Access to the server is granted only to authorized users for academic purposes.

Use Secure SSH Login: Always use secure SSH login with your username and password.

Protect Credentials: Do not share your login credentials with anyone. Unauthorized access is prohibited.

No Personal Data: Only store data relevant to your academic or research projects. Personal or sensitive data is prohibited.

Backup Regularly: Ensure important files are backed up. The server may undergo maintenance that could impact data availability.

Data Security: Do not attempt to access or interfere with other users' data or jobs.

Report Issues: Immediately report any security incidents or vulnerabilities to the server administrator.

Respect Maintenance Schedules: Regular maintenance times will be communicated. Log out during these times to avoid data loss.

Academic Use Only: The server is for educational and research purposes only. Commercial or personal use is prohibited.

Follow University Policies: Adhere to all university policies and guidelines related to computing resources.



### Consequences of Misuse

Account Suspension: Failure to follow these rules may result in temporary or permanent suspension of server access.

Academic Discipline: Misuse of resources may be reported and could lead to academic disciplinary action.



----------------------------------------------------------------------------------------------------------------



### Tutorial on Remote Login, Module Loading, and Job Submission with Slurm



### Prerequisites


- The SSH client is installed on your local machine.

- Access to a remote server with Slurm and module tools installed.

- A prepared job script (or familiarity with creating one).

- To Upload/Create/View/Modify Files, it is recommended to use FinalShell or FileZilla.



----------------------------------------------------------------------------------------------------------------


### Step 1: Login to the Remote Server


To Login via SSH:

1. Open a Terminal on your local machine.


2. Run the SSH Command:

   ssh username@hostname

   - Replace `username` with your remote server username.

   - Replace `hostname` with the IP address or domain name of the server.

   Example:

		ssh user123@cluster.example.com


3. Authenticate by entering your password if prompted. List of allowed commands will be displayed after login.


----------------------------------------------------------------------------------------------------------------


### Step 2: Load Required Modules


Modules often access specific software or libraries on a shared server (currently, only Anaconda is installed with the latest Torch, TensorFlow). You can also install packages with pip and create your python environment with conda.


Listing and Loading Modules:

1. List Available Modules:

		module avail

   - This command displays all modules available for loading on the server.


2. Load a Module:

   module load modulename

   - Replace `modulename` with the desired module name.

   Example:

		module load anaconda


3. Verify Loaded Modules:

		module list

   - Shows the list of modules currently loaded in your session.


----------------------------------------------------------------------------------------------------------------


### Step 3: Prepare a Slurm Job Script


There are two ways to submit jobs to SLURM.


#### Method 1: Run a Python script interactively:

1. From terminal,
		
		srun --gres=gpu:1 --time=00:30:00 -mem=500M python my_script.py

   
	Information:
	- `--gres=gpu:1`: Specifies 1 GPU.
	
	- `--time=00:30:00`: Sets a 30-minute runtime limit.
	
	- `--mem=500M`: Allocates 500MB of memory.
	
	This will run `my_script.py` and display output directly in the terminal.


#### Method 2 : To submit a python script to Slurm, you need a job script with specific configurations and commands. Below is a guide for creating a basic Slurm job script.

1. Create and Open a Script File using Vim or other editors (If using Vim, press 'i' to start editing):

		vim my_job_script.sh


2. Add Slurm and Command Lines:

		#!/bin/bash
		
		#SBATCH --job-name=my_job              # Job name
		
		#SBATCH --output=output_%j.txt          # Output file name with job ID
		
		#SBATCH --error=error_%j.txt               # Error file name with job ID
		
		#SBATCH  --gres=gpu:1                        # number of GPU
		
		#Load necessary modules
		
		module load anaconda
		
		#Run the job command
		
		python my_script.py


3. Save and Close the Editor. 

- If using Vim, press escape, type `:wq`, and press `Enter` to save and exit.


----------------------------------------------------------------------------------------------------------------


### Step 4: Submit the Job to Slurm


- To Submit the Job:

		sbatch my_job_script.sh

   -This submits the job to Slurm, which will schedule and execute it based on resource availability and job priority.


- To Check Job Status:

		squeue -u username

   -Replace `username` with your actual username. This command shows the status of your jobs.


- To View Job Output:

   -After the job completes, check the output file specified in your script (e.g., `output_<job_id>.txt`) for results.


----------------------------------------------------------------------------------------------------------------


### Step 5: Monitor and Manage Jobs


Common Job Management Commands:

- Cancel a Job:

		scancel <job_id>

   - Replace `<job_id>` with the ID of the job you want to cancel.



Note: Always confirm job parameters and resource requests as appropriate for your cluster's policies to avoid overloading shared resources.


----------------------------------------------------------------------------------------------------------------


### To use jupyter notebook with SBATCH:


1. SSH with -L with the jupyter port assigned to you (e.g., 9001)

		ssh user123@cluster.example.com -L 9001:localhost:9001


2. Add Slurm and Command Lines to the job script (e.g., my_job_script.sh):

		#!/bin/bash
		
		#SBATCH --job-name=my_job              # Job name
		
		#SBATCH --output=output_%j.txt          # Output file name with job ID
		
		#SBATCH --error=error_%j.txt               # Error file name with job ID
		
		#SBATCH  --gres=gpu:1                        # number of GPU
		
		#Load necessary modules
		
		module load anaconda

   #Run the job command

		jupyter notebook –no-browser –port=9001


3. To Submit the Job:

		sbatch my_job_script.sh


4. Find the jupyter URL with token from output.txt file

		cat output.txt


5. Use jupyter notebook from your local machine as normal, with the computation performed on the server.
