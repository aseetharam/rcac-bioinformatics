## Optimizing Trinity assembly on Bell/Negishi cluster

You will mostly likely hit the file limit if you are running Trinity on `/scratch` on Bell/Negishi cluster. Here are few recommendations to improve performance of your runs:

### 1. Running phase 2 of Trinity on `/dev/shm`

For the first 2 steps for Trinity (`inchworm` and `chrysalis`), number of files created are minimal. However, for the second phase (phase 2, `butterfly`), large number of files are created. You run this part of analyses on `/dev/shm` which is a memory based file system. It is faster than `/scratch` (a network drive) and does not have file limit. 

A typical command would look like this:

```bash
mkdir -p /dev/shm/trinity_workdir
mkdir -p $RCAC_SCRATCH/trinity_out_dir
Trinity \
   --seqType fq \
   --left reads_R1.fq.gz \
   --right reads_R2.fq.gz \
   --output ${RCAC_SCRATCH}/trinity_out_dir \
   --workdir /dev/shm/trinity_workdir \
   --CPU ${SLURM_CPUS_ON_NODE} \
   --max_memory 200G
```

Note that if you run out of walltime, the files in `/dev/shm` will be lost. So be sure to request enough wall-time for this job. 


### 2. Cleaning up intermediate files

Depending on your downstream analyses, you may want to reconsider saving intermediate files generated by Trinity. You can use the `--full_cleanup` option to delete the intermediate files. If there are failed runs in the butterfly step, it will prevent the cleanup of the intermediate files and you can resume the run from there.


### 3. Normalization of reads

By default, Trinity will enable _in silico_ normalization of reads. This is especially useful if your dataset is too large. If you are using `--no_normalize_reads`, you may want to reconsider and remove this option. It will reduce the memory usage and the runtime of the Trinity run, but not alter the quality of the assembly.


### 4. Running Trinity in multiple stages

Finally, if you prefer more control, you can decide to run them stepwise as shown on the [Trinity documentation](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Running-Trinity#running-trinity-in-multiple-sequential-stages). This will allow you to monitor the progress of the run and potentially restart from a failed step and archive the intermediate files as you progress.

```bash
 # just run the initial in silico normalization step and kmer counting:
 Trinity (opts) --no_run_inchworm

 # run through inchworm, stop before chrysalis
 Trinity (opts) --no_run_chrysalis

 # run through chrysalis, stop before Trinity phase 2 parallel assembly of clustered reads
 Trinity (opts) --no_distributed_trinity_exec

 # finish the job, running all Phase 2 mini-assemblies in parallel:
 Trinity (opts) 
 ```

### 5. Running Trinity on local storage of the node

If you are running Trinity on a smaller dataset (<100Gb), you can run Trinity on the local storage of the node. This will be faster than running on `/scratch`.  you will need to copy the files after they finish running.

```bash
cp $RCAC_SCRATCH/reads_R1.fq.gz /dev/shm/
cp $RCAC_SCRATCH/reads_R2.fq.gz /dev/shm/
cd /dev/shm
Trinity \
   --seqType fq \
   --left reads_R1.fq.gz \
   --right reads_R2.fq.gz \
   --output trinity_out_dir \
   --full_cleanup \
   --CPU $SLURM_CPUS_ON_NODE \
   --max_memory 200G
tar -czvf $RCAC_SCRATCH/trinity_out_dir.tar.gz trinity_out_dir
cp trinity_out_dir/Trinity.fasta $RCAC_SCRATCH/
```


## Job script

Here is a sample job script to run Trinity on Bell/Negishi cluster:

```bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=64
#SBATCH --partition=<partition-name>
#SBATCH --account=<account-name>
#SBATCH --time=4-00:00:00
#SBATCH --job-name=trinity_run
#SBATCH --output=bell-%x.%j.out
#SBATCH --error=bell-%x.%j.err
#SBATCH --mail-user=${USER}@purdue.edu
#SBATCH --mail-type=begin
#SBATCH --mail-type=end
ml purge
ml biocontainers
ml trinity
mkdir -p /dev/shm/trinity_workdir
Trinity \
   --seqType fq \
   --left reads_R1.fq.gz \
   --right reads_R2.fq.gz \
   --output trinity_out_dir \
   --full_cleanup \
   --workdir /dev/shm/trinity_workdir \
   --CPU $SLURM_CPUS_ON_NODE \
   --max_memory 200G
```

Note: replace `<partition-name>` and `<account-name>` with appropriate values.