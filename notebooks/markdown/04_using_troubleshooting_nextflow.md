# Using and Troubleshooting Nextflow
2025-05-14

Last class we introduced the basic concepts of Nextflow and demonstrated running some simple Nextflow pipelines. In today's class we are going to walk through some useful tips for using and troubleshooting Nextflow pipelines. 

## Table of Contents


[nf-core Continued](#nexflow-logs)<br>
[Nextflow Configuration File](#nextflow-configuration-file)<br>
[Test Profiles](#test-profiles)<br> 
[Troubleshooting](#troubleshooting)<br>
[Nextflow Logs](#nexflow-logs)<br>
[Workflow Diagrams](#workflow-diagrams)<br>
[Environment Variables](#environment-variables)<br>
[Error Strategies](#error-strategies)<br>
[Additional Resources](#additional-resources)<br>
[Recap](#recap)<br>





## nf-core Continuted

Last class we ended off running the nf-core/demo. We were having trouble with nextflow accessing your conda environment. This is due to conda not having proper access to your $PATH variable, and seems to be a specific Windows issues. We will troubleshoot in class today and if we can't fix it, please come to office hours or email us so we can help you get this set up. 


```python
nextflow run nf-core/demo \
   -profile conda \
   --input samplesheet.csv \
   --outdir nf_core_demo_output
```

If we run this, we will see the process create the conda environment needed to run this pipeline. 

We will also see an error if we don't have enough memory, an issue you are likely to encounter on your own laptop.

<img src="../../images/03_nf_core_error.png" width = 700>


In the nf-core/demo base config file , located under **conf/base.config** , we can see there is something going on with how processes are labelled, and the amount of memory allocated to them.

https://github.com/nf-core/demo/blob/1.0.1/conf/base.config#L22


<img src="../../images/03_nf_core_base_conf_memory.png" width = 500>




Our issue specifically with the **fastp** process.

So now if we look at the nf-core/demo github repository, the information is under modules/nf-core/fastqc/main.nf

https://github.com/nf-core/demo/blob/1.0.1/modules/nf-core/fastqc/main.nf#L3 

Here we can see that the process is labelled `process_medium`, 


<img src="../../images/03_nf_core_fastp_label.png" width = 800>




https://github.com/nf-core/demo/blob/1.0.1/conf/base.config#L33



In order to be able to run this on our computers, we will have to make a slight modification to the pipeline, and re-label this as `process_low` to reduce the memory requirements. 

<br>
<img src="../../images/lightbulb.png" width=55> 
To follow best practices, when we are making changes to a workflow we should make a copy of the nf-core/demo, either by forking the repository or copying it, and make these changes on a version controlled repository. However, we are just in troubleshooting mode, so let's see where we can edit this to test if this modifcation will be successful.

Let's open this file on our computers to edit the process label. **But where do we find this?**

<br>
<img src="../../images/lightbulb.png" width=55> 

Remember in the nextflow.io/socks example where Nextflow pulled the repository from Github? It is doing the same thing here from nf-core/demo repository. 


Nextflow stores the github repositories in this location on your computer:

- `~/.nextflow/.assets `

So since we are looking for the the `nf-core/demo` repository, and we know the file we want to edit is under `modules/nf-core/fastqc/main.nf` on this repository, then full path to the file is

`~/.nextflow/.assets/nf-core/demo/modules/nf-core/fastqc/main.nf`

Let's open this file now in VSCode so we can edit it(or your text editor of choice)


```python
code ~/.nextflow/assets/nf-core/demo/modules/nf-core/fastqc/main.nf 
```

Edit the process label and save the file.

<img src="../../images/03_nf_core_fastp_low.png" width = 800>

Now if we re-run the pipeline, we should have success! Let's use the `-resume` function we mentioned earlier to pick up where we left off!


```python
nextflow run nf-core/demo \
   -profile conda \
   --input samplesheet.csv \
   --outdir nf_core_demo_output \
   -resume
```

<img src="../../images/03_nf_core_success.png" width = 700>


## Nextflow Configuration File

As a Nextflow pipeline user, you may want to adjust some of the default settings to fit the purpose of your analysis. In our last lesson, we hinted towards there being a configuration file for Nextflow pipelines and looked at parameters. The `nextflow.config` file stores settings for how the pipeline should be run, including the parameters needed for the commands within processes. There can be a number of configuration files, but we will focus on the one found in the same directory as the `main.nf` (workflow) file. 

<img src="../../images/lightbulb.png" width=55> 
Any parameter specified on the command line at the time of running the pipeline overrides those defined in the `nextflow.config` file.
<br>
<br>
<br>
<br>
<img src="../../images/laptop.png" width=60>

Let's look at the Nextflow pipeline: https://github.com/BCCDC-PHL/WasteFlow

The process `lineage_freyja` uses lineage-defining mutations to quantify the relative amounts of SARS-CoV-2 lineages present in a wastewater sample. The software in this process `freyja` will only consider genomic sites covered by a minimum number of reads, specified with the `--depthcutoff` paramter. What is the minimum read depth currently set to? What implications for calling lineages might raising this number have?

The set up looks a bit different in nf-core community Nextflow pipelines, for example, a process references the `conf/modules.config`, which references the `nextflow.config`, but the principle is the same: The parameters for processes are specified in the `nextflow.config` file. Let's see what the minimum read length is for the long read filtering step in the [mag pipeline](https://github.com/nf-core/mag), which assembles and bins metagenomes. The process is called `FILTLONG`. Remember that you can override any parameters in the `nextflow.config` simply by adding them to your `nextflow run` command, as in `nextflow run --< parameter name > < your value>`. You can also find parameters for nf-core pipelines listed in their [docs](https://nf-co.re/mag/3.4.0/parameters/).

### Profiles

A `profile` is a collection of settings that can be activated simultaneously during pipeline execution. For example, you could define settings to run the pipeline in low memory mode by writing:


```python
low_mem {
        process {
            memory = '8 GB'
        }
    }
```

Then activating it during pipeline execution with `-profile low_mem`. We have seen the `-profile conda` or `-profile docker` which could appear like this in the `nextflow.config` file:


```python
profiles {
  conda {
    process.conda = 'samtools'
  }

  docker {
    process.container = 'biocontainers/samtools'
    docker.enabled = true
  }
}
```

## Test Profiles

To ensure software works as expected, it may come with a test dataset with known results which users can attempt to reporduce when setting up for the first time. This holds true for many Nextflow pipelines and is part of the nf-core [guidelines](https://nf-co.re/docs/guidelines/pipelines/recommendations/testing). Before running a pipeline on your real data, it is good practice to first run in with the `-profile test`. `Test` is one of the profiles that can be defined in the `nextflow.config` like the `docker` or `conda` profiles we saw earlier. It ensures any errors we see are not a result of our inputs, parameter choices, or typos, which helps narrow down any issues! You can test your pipeline is working like so:


```python
nextflow run nf-core/<pipeline_name> -profile test,docker,conda --outdir <OUTDIR>
```

Once your pipeline (hopefully) completes successfully, you can compare the outputs you have to the subset of total expected outputs that are hosted on the website associated with the pipeline (ex. [here](https://nf-co.re/genomeassembler/1.0.1/results/genomeassembler/results-3f1f59ddfbf92836cea735aff5a67b6212fed9f9/)). Now you can run your pipeline using real data, omitting the `test` profile as in the following example:


```python
nextflow run nf-core/methylseq -profile docker --input 'input_data/*.fastq.gz' --outdir myproj/results --genome GRCh38
```

If the test profile did not complete successfully, it is time to troubleshoot!


<img src="../../images/lightbulb.png" width=55> 

## Troubleshooting


You are faced with a screen full of red text. Now what? This is a natural part of bioinformatics that everyone using code at some point or another encounters. Please know that troubleshooting is difficult and try not to get discouraged. It can take a lot of time to figure out errors. Here are a few general starting points:

1. **Read the error message:** it *may* be descriptive and accurate enough that you will be able to correct it from this alone.
1. **Check the program's log files:** there is usually detailed info here. Often, software will have a verbosity parameter, so increasing the level of information can help.
1. **Resort to the internet:** odds are someone's seen this error before (stack overflow, GitHub issues page for the tool, µbioinfo and nf-core Slack channels are amazing resources to reach a community of people willing to help, ChatGPT)
1. **Make a minimal reproducible example:** is there a minimal amount of code/ files that allow you to reproduce the error? This is helpful to share with others (ex. on the internet) who will help you to troubleshoot. It is much easier to work with than a full pipeline and helps to narrow down the issue.
1. **Post your own issue on the Github repository of the tool:** If you can't find resources online, here are the [steps](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-an-issue) to create an issue. Make sure you include as much information as possible on how to reproduce your error (using the step above). Include input data where possible and the exact commands you used to produce the error. Note your tool versions and any system specificiations (ex. I'm running this on a linux HPC using SLURM). While walking through this, you may just solve your own error (said from personal experience).
1. **Take a break from it and revisit later:** sometimes all it takes is a fresh perspective!
1. **Find a buddy who you can talk through your issue with:** You may be "lost in the sauce" and explaining the issue to someone can help you see things from another perspective. See [rubber duck debugging](https://rubberduckdebugging.com/). 
1. **Try try try again again again**: If at first you don't succeeed..


Read through [this document](https://nf-co.re/docs/usage/troubleshooting/ ) for some further troubleshooting tips from nf-core.

## Nextflow Logs


Nextflow has a lot of files to help us track what went on where during the pipeline execution. 


<br>
<img src="../../images/lightbulb.png" width=55> 
The amount of information output in these files may seem overwhelming at first. Keep in mind, you will most likely never need to look at 99% of this information, but knowing where you can look when you encounter a pipeline issue is very useful. It takes time to develop your Nextflow senses so don't get discouraged by pipeline errors. With each error you solve, you will be that much more equipped for the next one. 
<br>
<br>




<img src="../../images/laptop.png" width=60>
<br>

If you run the `nextflow log` in the directory where you ran nextflow from, it will output useful information


```python
nextflow log
```

<img src="../../images/03_nextflow_log.png">

The names are self explanatory but
- `TIMESTAMP` is the time you ran Nextflow
- `DURATION` is the time Nextflow took to run
- `RUN NAME` is the run name used to refer to your Nextflow run execution 
- `STATUS` is the status of your Nextflow run. OK means it was successful, but it may say ABORTED, or FAILED if there was an issue with your pipeline.
- `REVISION ID` is the version of the Nextflow script that was run. We can see we ran the sample version of the hello_world.nf script
- `SESSION ID` is the Unique identifier (UUID) associated to the specific Nextflow run  execution
- `COMMAND` is the command that was run for your Nextflow execution

If you run `nextflow log` with the run name, you will get further information on the work directories

This is useful if you come back another day and don't have your terminal outputs saved to know the specific workflow directory that your pipeline output to. 


```python
nextflow log big_snyder
```

<img src="../../images/03_nextflow_log_work_dir.png">

### Some useful hidden files

In the directory where you execute your nextflow run, there are some hidden files


<img src="../../images/03_nextflow_hidden_files.png">

- `.nextflow` is a folder that holds most of the tracking information accessed by the `nextflow log` command

- `.nextflow.log` is a log created for each pipeline execution that contains all the inner workings of what went on behind the scenes of your pipeline execution. You don't really need to look at these unless something went wrong in your pipeline, and later on we will learn about the --with-report and --with-trace functions, which nicely compiles all of this information for you, but it is good to know that these files exist.


In the specific work directory of a process there are some more hidden files

<img src="../../images/03_nextflow_hidden_files_work_dir.png">


- `.command.sh` is a useful one - this tells you the specific script command that was executed. This is helpful for troubleshooting as you can run this script in the directory and can test and replicate errors
- `.command.err` will tell you more information on any errors encountered during the specific task
- `.command.log` will tell you the log info for the specific task
- `.command.out` will tell you the output printed to the terminal during a specific task

### nf-core pipeline_info logs

The nf-core community has made a lot of this easy for us.  In the output directory of all nf-core pipelines, there should automatically be a folder called `pipeline_info` that contains the following files:

- `execution_report_YYYYMMDD.html`: this produces a summary of the pipeline execution in an html report.
- `execution_timeline_YYYYMMDD.html`: this contains details on how long each process of the pipeline took to run, and the memory usage
- `execution_trace_YYYYMMDD.txt`: this contains detailed information each process that was executed during the pipeline run.
- `params_YYYYMMDD.json`: this contains information on the values of the pipeline parameters used during the pipeline execution
- `pipeline_dag_YYYYMMDD.html`: this contains a visual diagram of the steps of the nextflow pipeline (more on this file in the next section)
- `nf_core_pipeline_software_<tool>_versions.yml ` : this contains information on the software versions used during your pipeline executions


These contain a bunch of useful information about the command that was run, the locations of all the output files and work directories, and information on any issues that occured during your pipeline run. Most of the time, you won't need to look at these files, but when an issue does arise, they can be very helpful for troubleshooting. 

<img src="../../images/laptop.png" width=70>


Let's just take a quick look at the execution_report.html from our nf-core demo from last class. 


### Parameters for pipeline reports for non nf-core Nextflow pipelines
If you are running a pipeline that isn't from nf-core, you can add the following parameters to your pipeline to get the same outputs for any nextflow pipeline run. These should be output in the top level of your output directory. 

- `-with-report  <file_name>.html`
- `-with-timeline <file_name>.html`
- `-with-trace <file_name>.txt`
- `-with-dag <file_name>.png`   (more details on this parameter in the next section!)

**Note**: `<file_name>` can be whatever you wish the file to be called. 



Find more information on these outputs [here](https://www.nextflow.io/docs/latest/reports.html)


## Workflow Diagrams


### nf-core 
When you're running nextflow pipelines, it's important to have idea of what the pipeline is doing, and be able to share that with others easily. A way to do that is with Directed Acyclic Graphs (or DAGs). All nf-core pipelines should have this within the `pipeline_info` folder that is present in your output directory (the directory you set with the `--outdir` parameter).

-  ex. `outdir/pipeline_info/pipeline_dag_2025-05-10_11-03-32.html`

<img src="../../images/04_pipeline_dag_demo.png" width=800>

### --with-dag parameter

Nextflow also has a parameter called `-with-dag` that you can use with most other pipelines. **Note:** this may not be compatible with nf-core pipelines as they already have this output)

**Note**: you need to have graphviz installed in your environment to produce a DAG with this parameter, and conveniently enough we have this in our virtual environment.

<img src="../../images/laptop.png" width=60>

Let's test this out with the BCCDC-PHL/bioinflow pipeline. We will use the `-preview` parameter to view the DAG without actually running the pipeline 



```python
nextflow run BCCDC-PHL/bioinflow \
--input question.txt \
--name <your name> \ 
--outdir dag_test \
-with-dag flowchart.png \
-preview 
```

<img src="../../images/04_flowchart.png" width=800>

The `flowchart.png` file is output in the directory where the pipeline was executed. You can put the full file path in the argument for the parameter  if you want to specify where the file will be output
We can see this is a very basic depiction of the pipeline processes and may or may not be helpful depending on how complicated the processes are. 


### Other options

In such cases where the first two options don't produce useful diagrams, there are tools you can use to make your own custom diagram
- Mermaid diagrams: https://mermaid.live/ . Mermaid is a very simple markup language that allows us to create workflow diagrams. The syntax is very straightforward, and you can use mermaid in markdown to generate simple diagrams. We will cover more on how to use Mermaid diagrams when we cover Documentation in later classes.
- draw.io : https://app.diagrams.net/ is a free online diagram software for making flowcharts, process diagrams, org charts, UML, ER and network diagrams.






## Environment Variables 

Sometimes pipelines you want to run were designed with earlier versions / [releases](https://github.com/nextflow-io/nextflow/releases) of Nextflow than you may have downloaded. Not to worry! Nextflow has a handy parameter called `NXF_VER` where you can specify the nextflow version you want to use and it will run your pipeline execution with that version. The next time you run Nextflow without that paramter, it will go back to using your usual version. 



```python
NXF_VER=<release version> nextflow run <pipeline or nextflow script> 
```

For example


```python
NXV_VER=24.04.4 nextflow run hello-world.nf
```

## Error Strategies

By default, if one process in your pipeline fails, then the entire pipeline will fail. There may be cases when you want Nextflow to continue on when a process fails.

There are different ways to handle errors by adding `errorStrategy '<value>` at the top of your process.

### Ignore

The first way is to just ignore the error. If the process fails, the pipeline will continue on to the next step. You may want to use this for optional processes where the output is not used as input by other processes.


```python
process assemble_that_genome {
    errorStrategy 'ignore'

    script:
    """
    optional_genome_assembly.py
    """
}
```

You can set this as the default error strategy for all processes by adding `process.errorStrategy = 'ignore'` to your nextflow.config

###  Retry

The next option is to retry a process if it fails. This is the eqivalent of turning it off and back on again. You may want to use this if you have a finicky tool where re-running it seems to usually do the trick.


```python
process assemble_that_genome {
    errorStrategy 'retry'

    script:
    """
    finicky_genome_assembly.py
    """
}
```

You can also have it retry the process multiple times by using `maxRetries`


```python
process assemble_that_genome {
    errorStrategy 'retry'
    maxRetries 4

    script:
    """
    finicky_genome_assembly.py
    """
}
```

### Retry with backoff

If you want to introduce a delay between retries when a process fails in Nextflow, you can use the errorStrategy shown below. It implements an exponential backoff, meaning that the wait time doubles with each failed attempt before the process is retried. You may want to use this when you are accessing something on from a network, ex. downloading fastqs from SRA, where the error could be due to network connection issues, and waiting and trying again could solve the failure.


```python
process assemble_that_genome {
    errorStrategy { sleep(Math.pow(2, task.attempt) * 200 as long); return 'retry' }
    maxRetries 3

    script:
    """
    delayed_genome_assembly.py
    """
}
```

## Additional Resources

- **Troubleshooting**: Check out this training resource for some common Nextflow errors and their solutions: https://training.nextflow.io/2.0/troubleshoot/ Note: Gitpod is no longer functional with this resource unfortunately, but it still has some great information.
- **Awesome pipelines**: Check out this list of awesome pipelines: https://github.com/nextflow-io/awesome-nextflow

- **Sequera AI**: Tailored AI for your Nextflow help (free but requires an account): https://seqera.io/ask-ai/chat  They also have a VSCode Copilot Extension! 

## Research Question

You should now know the Nextflow pipeline you will be using to help answer your research question for your class project. Hopefully, you have had a chance to try your selected pipeline out with the test profile or a toy dataset. We will take some time now to consider the pipelines you want to use for your class project and try setting them up. If you encounter errors, try resolving them with the steps outlined in this lesson - Let's take some time now to troubleshoot together!

Remember, it is tempting to want to get as many answers from as much data as possible as quickly as possible, but oftentimes a minimal dataset analyzed well will yield more informative results and more efficiently than one that was not as well-defined or precise. 

## Recap 

You now know how to ***use*** Nextflow pipelines and the process for ***troubleshooting*** them, including ***.nextflow.log***. You have learnt the anatomy of a Nextflow pipeline and how to navigate them to find ***parameters*** in the ***nextflow.config*** file. 
