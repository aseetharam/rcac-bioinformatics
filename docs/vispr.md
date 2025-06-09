# MAGeCK-VISPR visualization guide

MAGeCK-VISPR is a comprehensive quality control, analysis, and visualization workflow for CRISPR/Cas9 screens. This guide provides a straightforward method for running the visualization component on an HPC cluster and viewing the results on your local machine.

## 1. Connect to HPC and start an interactive session

First, open a terminal on your local computer and connect to the HPC cluster. Then, request an interactive session on a compute node.

```bash
# Connect to a Purdue RCAC HPC cluster (e.g., Negishi)
ssh your_username@negishi.rcac.purdue.edu

# Request an interactive node with 8 cores for 4 hours
# Replace "your_account" with your specific account/allocation name
salloc -N 1 -n 8 -t 4:00:00 -A your_account
```

Once the job is allocated, you'll be automatically connected to a compute node. Take note of the node's name (e.g., `a409`) as you will need it later.

You can also do `echo $SLURM_JOB_NODELIST` to see the node name.


## 2. Load the modules and start the VISPR Server

Next, load the required software modules and navigate to the directory containing your MAGeCK-VISPR results.

```bash
# Load the necessary modules
ml biocontainers
ml mageck-vispr

# Navigate to your project directory
cd /path/to/your/mageck-output

# Start the VISPR server using the Apptainer container
# This command finds all .vispr.yaml files in the 'results' subdirectory
apptainer exec /apps/biocontainers/images/quay.io_biocontainers_mageck-vispr\:0.5.6--py_0.sif vispr server results/*.vispr.yaml
```

The server will start and indicate it's running on a specific port, usually `5000`. **Leave this terminal running.**

```{note}
The session should remain active and should not display any errors. Warning messages are normal, but if you see errors, please check the module versions or your input files.
```


## 3. Forward the Port to your local computer

To view the web interface, you need to create an SSH tunnel from the compute node to your local machine.

Open a **new, second terminal** on your local computer and run the following command.

```bash
# Replace <node_name> with the name of the node from step 1 (e.g., a409.negishi.rcac.purdue.edu)
# Replace <port> with the port number from the server output (e.g., 5000)
# Replace <your_username> and <hpc_login_node> with your details
ssh -L <port>:localhost:<port> -J <your_username>@<hpc_login_node> <your_username>@<node_name>
```

**Example:**

If your job is on node `a409`, the server is on port `5000`, your username is `pete`, and you log into `negishi.rcac.purdue.edu`, the command would be:

```bash
ssh -f -N -L 5000:localhost:5000 -J pete@negishi.rcac.purdue.edu pete@a409.negishi.rcac.purdue.edu
```

```{warn}
You will be asked to enter your password for the HPC login node (or use the ssh-key if you have set it up). Since you will likely be connecting to the compute node for the very first time you will also be asked to confirm the authenticity of the host. Type "yes" to continue.
```

## 4. View the VISPR Interface
After successfully establishing the SSH tunnel, you can now access the VISPR web interface from your local machine.

Open a web browser and navigate to:

```
http://localhost:5000
```

(Replace `5000` with the port number your server is using). You should now see the interactive VISPR interface.