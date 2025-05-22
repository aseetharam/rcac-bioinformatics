# Transfer data with iRODS


CyVerse provides a cloud-based data store accessible via iRODS, allowing researchers to manage and transfer large datasets. From RCAC clusters (Negishi, Bell,  Anvil), you can authenticate with your CyVerse account and use iCommands to list, upload, or download files efficiently. 


## Setting up iRODS access to CyVerse

Before using iCommands, you need to load the appropriate modules and initialize your iRODS environment. This is a one-time setup process.

### Step 1: Load required modules

```bash
module --force purge
module load biocontainers
module load irods
```

* `--force purge` clears any previously loaded modules to avoid conflicts.
* `biocontainers` and `irods` provide access to iCommands like `iinit`, `iget`, `iput`, etc.


### Step 2: Initialize your iRODS session

Run:

```bash
iinit
```

You will be prompted to enter the following:

```
Enter the host name (DNS) of the server to connect to: data.cyverse.org
Enter the port number: 1247
Enter your irods user name: <your_cyverse_username>
Enter your irods zone: iplant
Enter your irods password: <your_cyverse_password>
```

Enter your CyVerse password when prompted. The password is not displayed (you won't see `*` or `.` either).

If successful, this creates or updates a configuration file (usually at `~/.irods/irods_environment.json`), enabling seamless use of other iCommands.

```{warning}
If you don’t have a CyVerse account, register at [https://user.cyverse.org/](https://user.cyverse.org/)
```



### Step 3: Verifying your iRODS connection

After completing the setup with `iinit`, you can verify that you're connected to the CyVerse Data Store by running the following commands.

#### Check your current iRODS directory

```bash
ipwd
```

This should return a path like:

```
/iplant/home/<your_cyverse_username>
```

#### List files and directories in your Data Store

```bash
ils
```

You should see a listing of your CyVerse files and folders. Example output:

```
/iplant/home/<your_cyverse_username>:
  C- /iplant/home/<your_cyverse_username>/project1
  C- /iplant/home/<your_cyverse_username>/raw_data
    data_summary.txt
```

If these commands work and return expected output, your connection to CyVerse is successful and you’re ready to transfer files.




## Transferring files between RCAC and CyVerse

Once connected, you can use iRODS commands to move files between your RCAC environment (e.g., Anvil) and CyVerse Data Store. Below are the most common operations:

### Upload a file from RCAC to CyVerse

```bash
iput results.txt /iplant/home/<your_cyverse_username>/
```

This command uploads the local file `results.txt` to your CyVerse home directory.

### Upload a directory (recursively)

```bash
iput -r analysis_folder /iplant/home/<your_cyverse_username>/
```

The `-r` flag allows you to upload entire directories.


### Download a file from CyVerse to RCAC

```bash
iget /iplant/home/<your_cyverse_username>/raw_data.fastq .
```

This downloads the file into your current working directory on the cluster.

### Download a directory (recursively)

```bash
iget -r /iplant/home/<your_cyverse_username>/project_folder .
```

This downloads an entire directory and its contents.


### Create a new directory (collection) in CyVerse

```bash
imkdir /iplant/home/<your_cyverse_username>/new_project
```

Organize your files by creating collections before uploading.


### Rename or move files within CyVerse

```bash
imv /iplant/home/<your_cyverse_username>/old.txt /iplant/home/<your_cyverse_username>/archived.txt
```

This renames `old.txt` to `archived.txt` in place.


### Delete files or directories from CyVerse

```bash
irm /iplant/home/<your_cyverse_username>/unnecessary.txt
irm -r /iplant/home/<your_cyverse_username>/old_project
```

Use `-r` to delete directories. These operations are permanent unless your account uses trash recovery.



## Commonly used iRODS commands with descriptions and usage examples:



| Command     | Description                                             | Example Usage                                                                 |
|-------------|---------------------------------------------------------|--------------------------------------------------------------------------------|
| `iinit`     | Authenticate and start an iRODS session                 | `iinit` (follow prompts: hostname = `data.cyverse.org`, port = `1247`, etc.)  |
| `ils`       | List current files and directories                      | `ils`<br>`ils /iplant/home/your_username`                                     |
| `ipwd`      | Show current iRODS working directory                    | `ipwd`                                                                         |
| `icd`       | Change directory (collection) in iRODS                  | `icd /iplant/home/your_username/project1`                                     |
| `iget`      | Download a file or directory from iRODS to local system | `iget data.txt`<br>`iget -r project_folder`                                   |
| `iput`      | Upload a file or directory from local system to iRODS   | `iput results.txt /iplant/home/your_username/`<br>`iput -r output_dir`        |
| `imkdir`    | Create a new directory (collection) in iRODS            | `imkdir /iplant/home/your_username/new_project`                               |
| `irm`       | Delete files or directories from iRODS                  | `irm old.txt`<br>`irm -r old_project`                                         |
| `imv`       | Move or rename files/directories within iRODS           | `imv oldname.txt newname.txt`<br>`imv old_dir/ new_dir/`                      |
| `icp`       | Copy a file or directory within iRODS                   | `icp file1.txt file2.txt`<br>`icp -r project1/ project2/`                     |
| `ichksum`   | Compute or verify file checksums                        | `ichksum results.txt`                                                         |
| `ienv`      | Display current iRODS session environment               | `ienv`                                                                         |
| `ihelp`     | Show list of available iCommands                        | `ihelp`                                                                        |
| `iexit`     | Log out and clear iRODS session                         | `iexit full`                                                                   |



## References

- [iRODS Documentation](https://irods.org/documentation/)
- [CyVerse iRODS Documentation](https://learning.cyverse.org/ds/icommands/)
