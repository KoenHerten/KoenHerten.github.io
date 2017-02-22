---
layout: post
title:  "VSC launching batch jobs"
date:   2017-02-22 14:50:00 +0100
categories: discussion
---

Since my "developmental phase" on the [VSC](https://www.vscentrum.be/) is slowly going into a more "production phase", I'm starting to launch bigger an bigger projects and datasets. When executing exactly the same scripts for multiple samples, it is not visible to change each script for each sample. Therefor I need a way to launch these batches of samples. From the VSC they support multiple options, but there are also some other "not" supported options possible:
* [worker framework](https://github.com/gjbex/worker)
..* This framework is specially developed by the people @ the VSC
..* PRO: 
....*You do not need to take care of anything special, simply create the PBS script, use a variable like SAMPLE for the samplename, and have a file list, with as header SAMPLE and each line the name of a sample
..* CON: 
....* I experienced a lot of troubles when the list goes over 60. 
....* There is a threaded option, but does not always act like discribed. Sometimes when I ensure that each job runs on a node (20 cores, so 20 threads supplied). I end up with nodes running multiple jobs, and empty nodes. 
....* Moreover in combination with some tools I got a message that the job was finished, while the data was still generated (could be a combination of worker and tool, but did not observe it with the other methods).
* [atools](https://github.com/gjbex/atools)
..* This framework is developed by the same person as the worker framework, but is a wrapper around the regular batch submission system of the schedular
..* PRO: 
....* The framework is a wrapper around traditional batch jobs so only 1 job ID is visible in the qstat and other commands. 
....* Moreover if the notification by mail is activated, only 1 mail is recieved.
..* CON: 
....* Costs can go quite high, since the requests in the pbs script is for each job seperate, while in the worker is for everything together. 
....* There is a limit on the number of jobs you can submit in total, not, which is on the VSC quite low, project with more than 60 samples could not be submitted in 1 go (even more, I could not submit more jobs, untill a few of these had finished).
* The provided batch system included in the schedular
..* This is simple launched through:
```bash
qsub -t 1:20%10 job.pbs
```
...Here the array goes from 1 to 20, while maximum 10 jobs are executed at the same time
..* PRO: 
....* In comparison with the 2 before, the complete management of the jobs is done by the schedular
..* CON: 
....* In all commands to check the jobs, all tasks are shown. This with the general job id, followed by an array index. 
....* When notification through mail is asked, a mail per task is received.
* [GNU parallel](https://www.gnu.org/software/parallel/)
..* This is a shell framework.
..* PRO: 
....* 1 pbs script for all tasks, similar as the worker framework. 
....* not limited to changing only a few variables (worker), or 1 arrayindex (atools, regular batch system). Since multiple different sh scripts could be launched
..* CON:
....* need some "advanced" knowledge of bash programming for usage over multiple nodes
```bash
#example for on 1 node:
#get the number of cores in this node (optimization)
CORES=`nproc`
#commands.txt is a file containing all sh scripts which should be executed
#{} will be the path of the sh file, the sh file will be executed, and a log and error file will be created
cat commands.txt | parallel -j $CORES 'sh {} > {}.log 2> {}.err'
```
```bash
#example for multiple nodes, 1 task per node (can be changed to multiple tasks by changing the -j option like above)
#get the list of nodes reserved by this job (each node should only be mentioned once):
cat $PBS_NODEFILE | sort | uniq > nodefile

#setting parallel environment, so that all modules loaded and all environment variables of the pbs script are also available on the other nodes
export PARALLEL="--workdir . --env PATH --env LD_LIBRARY_PATH --env LOADEDMODULES --env _LMFILES_ --env MODULE_VERSION --env MODULEPATH --env MODULEVERSION_STACK --env MODULESHOME --env OMP_DYNAMICS --env OMP_MAX_ACTIVE_LEVELS --env OMP_NESTED --env OMP_NUM_THREADS --env OMP_SCHEDULE --env OMP_STACKSIZE --env OMP_THREAD_LIMIT --env OMP_WAIT_POLICY";
#end setting parallel environment

cat commands.txt | parallel --sshloginfile nodefile -j 1 'sh {} > {}.log 2> {}.err'
```

As a conclusion I think worker is the prefered system for most standard, small scale users with low HPC and programming experience. However when you want to perform large jobs, with large amount of data, and a high number of repeating jobs, worker seems to reach some limitations. Therefor I will keep on using worker in the teaching for new users (see also my workshop [VSC NGS workshop](https://github.com/GenomicsCoreLeuven/vsc_ngs_workshop)). For my own tools and scripts I switch to the parallel implementation (currently only available in the DEV branch of my [pika](https://github.com/GenomicsCoreLeuven/pika) tool).
