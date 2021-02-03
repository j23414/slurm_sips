# Slurm Sips

Exploring slurm in small examples.

Log onto an HPC configured for slurm.

```
ssh yourname@somewhereout.there
```

Anything that can be run in command line, can be placed in a slurm script.

<details><summary>Hello World Example</summary>

**bash**

```
echo "Hello World"
#> Hello World
```

Wrap it in a slurm script by saving the following in a `script.slurm` file.

```
#! /usr/bin/env bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=16
#SBATCH --time=24:00:00
#SBATCH --job-name=HelloWorld
#SBATCH --output=R-%x.%J.out
#SBATCH --error=R-%x.%J.err
#SBATCH --mail-users=yourname@email.com
#SBATCH --mail-type=begin
#SBATCH --mail-type=end
#SBATCH --account=your_project_name

echo "Hello World" > hello_world.txt
```

Submit

```
sbatch script.slurm
#> ###### (job id here

squeue | less 
squeue | grep username
```

</details>

<details><summary>Parallel example</summary>

```
touch a_01.txt a_02.txt b_03.txt
```

```
module load parallel
parallel parallel -j4 "sleep 5; ls -l {} > {}.out" ::: a_*.txt
```

```
sbatch submit.slurm
sacct --format=User,JobID,Jobname,partition,state,elapsed,CPUTime,MaxRss,MaxVMSize,nnodes,ncpus,nodelist -j 5518540
```

</details>


* Measure Memory Usage (`sacct`)
* Number of threads used (`ps`, `top`... `dask`?)
* Pick a partition based on memory/threads (`sinfo`)
* sbatch scripts taking an argument
* Dependent sbatch scripts (workflows, snakemake, nextflow, cwl?)
* `$TMPDIR` and other memory optimization tips (JVM, node partitions)
* Using a debug node to debug a pipeline (early cancel jobs, make sure executables are linked, then let it run)
