# Setting Up a Development Environment
2025-05-07

## Table of Contents
[Project Management](#project-management)<br>
[Integrated Development Environment](#integrated-development-environment)<br>
[Command-Line Interface](#command-line-interface)<br>
[Virtual Environments](#virtual-environments)<br>
[Version Control](#version-control)<br>
[Github Account & Repository Set Up](#github-account--repository-set-up)<br>
[Research Question Brainstorm](#research-question-brainstorm)<br>
[Recap](#recap)<br>

The first step in answering biologically-relevant questions with informatics is preparing an environment equipped with the necessary programming languages, software, and Integrated Development Environments (IDEs) that consolidate code and data. A *development environment* allows us to efficiently write, test, and reproduce code to analyze input data. In this class, we will set up a bioinformatics toolkit to set us up for success!

## Project Management

Essential to organizing bioinformatic projects are software that:

|Category| Function | Examples|
|:-----|:------|:---|
|package managers| help to install & remove tools and dependencies| conda, mamba, homebrew|
|workflow managers | organize steps/ commands of an analysis | Nextflow, WDL, Snakemake|
|workload managers | help organize how computational resources, usually on a High Performance Computing ([HPC](https://www.hpc.iastate.edu/guides/introduction-to-hpc-clusters/what-is-an-hpc-cluster)) cluster, are allocated for different analyses | SLURM, Sun Grid Engine|
|version control| manage different versions of the same code | Git|

## Integrated Development Environment

How do we manage all of the above? This is where *Integrated Development Environments* (IDEs) factor in. As the name suggests, IDEs help to integrate the different components of development, such as editing code (editor), executing it (console), interacting with the operating system of the computer (terminal), navigating the file system (file explorer), and version control or linting (extensions). Examples of popular IDEs include Visual Studio Code, Visual Studio Codium (open source), RStudio, PyCharm, and Eclipse. 

<img src="../../images/laptop.png" width=55> 

In BCHM5420 we will be using VS Code, which you can download here:

https://code.visualstudio.com/download

Some helpful extensions to install:

<img src="../../images/02_extensions.png" width="400">
 
- data wrangler
- peacock
- black formatter 
- python/ python debugger
- jupyter 
- R/ R debugger
- nextflow
- rainbow csv
- flamegraph

**Any other recommendations?**


If you ever get lost in VS Code, resort to Ctrl+Shift+P (Windows) or Cmnd+Shift+P (Mac) which will open the *command palette* and allow you to search for commands:

<img src="../../images/02_command_palette.png" width="400">



## Command-Line Interface

The CLI is an efficient way to interact with the computer. For example, instead of individually creating 10 folders, we can execute the following one-liner: 




```python
for i in {1..10}; do mkdir folder_${i}; done

ls
```

Likewise we can remove these folders with one swift command:


```python
rm -r ./*folder_*
```

### Components of a Computer

When you want to see the files in a directory, what happens to the `ls` you type on your laptop?

Firstly, you could open file Finder and look at the files in a folder, which would be using the *Graphical User Interface* (GUI).

<img src="../../images/02_gui_files.png" width=400> 

Another option would be to interact with the shell and subsequent components through the *Command-Line Interface* (CLI). 

You type using *external hardware* like your keyboard into the *terminal app* which is a text-based interface connecting you to the *shell*. Once the shell receives the command `ls` from terminal, it will convert this command into a kernel-understandable form and pass it to the *kernel*. The kernel bridges the shell and *internal hardware* of a computer, like the CPU & RAM, and will load the `ls` binary into RAM, schedules the process to run on CPU, and set up a standard output/ result (which will be passed back to you in terminal). The kernel and `ls` program interact to retrieve directory information from disk and voilà! A list of files in the current directory appears in terminal. 

The shell & kernel are both part of the *operating system* (OS) which comes in a variety of flavours such as Windows, Mac, Android, and Linux. Similarly, different operating systems will have different shells (powershell, Zsh, ABD, BASH) and kernels (Windows NT kernel, Mac XNU kernel, and Linux kernel). There are numerous terminal applications, sometimes referred to as "terminal emulators" as an homage to [older computers](https://www.howtogeek.com/unix-terminal-history-how-video-killed-the-printer-star/), like Hyper, GNOME terminal, PuTTY, Termux, the new AI-integrated [warp](https://www.warp.dev/), or VS Code terminal.   

<img src="../../images/02_computer_components.png" width=750 height=450> 

What type of shell are you using? `echo $SHELL`

### PATH environment variable

Now that we have a better understanding of the elements involved in CLI, let's discuss a few ways to make our lives easier when executing commands like utilizing ssh key pairs, aliases, and environment variables (ENV_VARIABLES are capitalized). We already saw an environment variable earlier (`$SHELL`). One important environmental variable is `$PATH`. This tells your operating system where (or which *paths*) to find executable programs. 



```python
echo $PATH

#/usr/local/bin:/usr/bin:/random/directory:/home/user/miniconda3/bin
```


In the above example, when you run the command `python` the shell checks, in order, the following directories for the executable and stops once it is first found:

1. /usr/local/bin
2. /usr/bin
3. /random/directory
4. /home/user/miniconda3/bin



<img src="../../images/laptop.png" width=55> 

Open a new terminal and type `echo $PATH`. Now type `PATH=/test/bioinfo/dir:$PATH` then `echo $PATH` again.
You should see /test/bioinfo/dir: followed by the other directories in your $PATH variable. 

What happens to the directory we added when you close the terminal, reopen it, and type `echo $PATH`?

$PATH or any environment variable is set in the current shell session only. For an environment variable to persist beyond the current shell, we can store it in the **~/.bashrc** file like this:

`export ENV_VAR=/test/bioinfo/dir:$PATH` 

The next time a new terminal is opened and we type `echo $ENV_VAR` we should get the same value. Alternatively, we can put this persistent change into effect in the same shell (ie. without opening a new terminal) by running the command `source ~/.bashrc`. 

**Aliases** are command shortcuts that can be stored in the ~/.bashrc file in a similar way. For example, if you are working on a project with a particularly cumbersome path, you can specify `alias bioinfo='/very/long/nested/path/to/the-directory/where/advanced/bioinformatics/is/located'` then `source ~/.bashrc` for the alias shortcut to take effect. Now you can run `cd bioinfo` to navigate to the alias value. Another common alias is `alias ls='ls --color=auto'`.


### File Naming Best Practices

Directory structure, project organization, and file naming take time to improve and get a hang of but some helpful guidelines are: 

1. NO SPACES (<img src="../../images/yes.png" width="20">camelCaseDir, snake_case_dir, kebab-case-dir vs. <img src="../../images/no.png" width="16">pls no dir)

![](../../images/02_pls_no.gif)
<div style="font-size: 12px">
bioinformaticians when they see directories named with spaces
</div>

2. Consistent (<img src="../../images/yes.png" width="20">dir_1, dir_2, dir_3 vs. <img src="../../images/no.png" width="16">dir-1, Dir2, third_directory)

3. Descriptive (<img src="../../images/yes.png" width="20">flu_a_fasta vs. <img src="../../images/no.png" width="16">my_data)

4. ISO 8601 format: YYYYMMDD or YYYY-MM-DD (<img src="../../images/yes.png" width="20">20250412_multi.fasta vs. <img src="../../images/no.png" width="16">Apr1225_multi.fasta)

5. Reliant on Version Control (<img src="../../images/yes.png" width="20">file - tracked with Git vs. <img src="../../images/no.png" width="16">file_1, file_1_final, file_1_final_THISISIT) 

## Virtual Environments 

The tools and libraries used in a project are installed in *virtual environments* which can be thought of as isolated "containers". Examples of virtual environments are conda, venv, and slightly more robust but still an isolated container, docker. The reason for this isolation from the global or *base* system environment is to: 

- prevent version conflicts of the same tools or packages
- manage dependencies for different tools 
- make projects reproducible by keeping tool versions consistent and allowing any user to set up the same environment

<img src="../../images/laptop.png" width=55> 

With that being said, BCHM5420 will use the following virtual environment which can be downloaded and set up here:

### Conda


```python
curl -O https://raw.githubusercontent.com/uleth-advanced-bioinformatics/BCHM5420A-summer-2025/refs/heads/main/resources/advanced_bioinfo_env.yml

conda env create -f advanced_bioinfo_env.yml

conda activate uleth_advanced_bioinfo

nextflow -v
```

### Docker 

If you have ever tried setting up someone else's analysis or pipeline, you will know the nightmare that is dependency installation. Docker simplifies this! You know when you go to bake cookies at someone else's house but the oven settings are all out of whack and they do not have the right sugar, the butter is salted instead of unsalted, and worst of all, they have raisins instead of chocolate chips - it just will not work. If only you had a portable mini version of your kitchen.. Enter Docker. Imagine the cookie is some software you are trying to get working and the ingredients that go into successfully ~~baking~~ making it will vary from system to system. Docker is a stripped down, mini, virtual OS that shares the kernel of your actual [OS](#components-of-a-computer) (if it is linux, otherwise virtualizes a linux kernel) but does not interfere with the rest of it. In this way, Docker *containerizes* everything you would need to perfectly reproduce the successful installation of your software applications. 

How do we know what to include in this container? Will you be needing a blender or a spoon? The container is run from an image which is built from a text file of instructions called a *dockerfile*. Referring back to our cookie metaphor, there is a list of things to bring on paper which you then take a photo of so you can let five other people see what brand of oven they should buy in order to perfectly replicate the mini portable kitchen required to make the [absolute best chocolate chip cookies](https://www.modernhoney.com/levain-bakery-chocolate-chip-crush-cookies/). 

<img src="../../images/02_docker.png" width=800 height=300>

You can find a collection of pre-existing docker images reposited at 
[<img src="../../images/02_docker_hub.png" width=100>](https://hub.docker.com/). 

In order to use these or build our own, we first install [Docker Desktop](https://www.docker.com/get-started/). If you are hard core for the CLI, you can follow [these steps](https://docs.docker.com/desktop/setup/install/mac-install/#install-from-the-command-line). 

<img src="../../images/02_docker_install.png" width=600>


Next, click on the Docker app and enter your password to grant access to allow a daemon to run in the background.

<img src="../../images/02_docker_click.png" width=600>

If you skip this step, you may see an error similar to this: 

<img src="../../images/02_docker_no_permissions.png" width=800>


When you open your terminal emulator, try running the following command to confirm installation was successful:


```python
docker run hello-world
```

If it was successful you will see an output resembling:

<img src="../../images/02_docker_hello_world.png" width=600>

Now may be a good time to install the docker extension in your IDE, VS Code. This integrates docker into VS Code and facilitates working with it. Remember the [command palette](#integrated-development-environment) in VS Code to access the most common docker commands. Notice the hello-world container from the command we just ran:

<img src="../../images/02_docker_vscode_extension.png" width=400>


One thing to note is that the default of docker is to start the container containing the necessary software (ex. nextflow), run the command (nextflow -v), then exit the container, unlike a conda where you "enter" into the environment and use the software interactively in a shell. In docker, the interactivity is specified by the ENTRYPOINT directive in the dockerfile. In the nextflow container, ENTRYPOINT ["nextflow"] does not start an interactive shell but rather runs nextflow immediately. Usually, you could use `-it` to start an **i**nteractive **t**erminal in `docker run -it nextflow/nextflow` ***if*** the ENTRYPOINT was not specified to auto-run a program. Since the nextflow container is hardwired to immediately run nextflow, we need to override this behaviour with `--entrypoint /bin/bash`. Notice the many nextflow:24.10.2 "individual containers" on the left of the terminal window in the image below - remember, containers are meant to be ephemeral and quick to spin up as-needed. 

<img src="../../images/02_docker_interactive.png" width=900 height=140>


While docker takes care of differences in OS (ie. Windows vs. MacOS vs. Linux) there is one thing it cannot change - the CPU architecture of your laptop or ***internal hardware***. For example, the CPU architecture of a Windows computer (windows/amd64) is distinct from a Macbook with Intel chip (linux/amd64) which is different from a Macbook with M1/M2 chip (linux/arm64). When building or running a container image, you can tell docker which architecture to use with the  `--platform` flag. Let's build our docker container!


```python
# build a docker image from a Dockerfile
# docker buildx create --name multiarchbuilder --driver docker-container --use
# docker buildx build --platform linux/arm64 -t uleth-advanced-bioinfo:1.0.0 --load .
# docker buildx build --platform linux/arm64,linux/amd64 -t uleth-advanced-bioinfo:1.0.0 --push .

docker pull j3551ca/uleth-advanced-bioinfo:1.0.0

# run a docker container from docker image - should be in the current working directory
docker run -it j3551ca/uleth-advanced-bioinfo:1.0.0

# run the docker image with a mounted volume -v <host_dir>:<container_dir>
docker run -it -v </the/dir/on/your/comp/you/want/in/the/container>:/home/container/docker_portal j3551ca/uleth-advanced-bioinfo:1.0.0
```

We could interactively use the container from CLI (:/home/container# prompt in above picture) or we can open the command palette in VS Code to attach to running container in a new window and use the IDE from within it:

<img src="../../images/02_docker_attach.png" width = 500>

We test that the python venv is working by pasting the following into a code cell of the /home/container/test/test.ipynb file, selecting our .venv environment, and running the following:


```python
import pandas as pd

for i in range(10):
    print(i)
    
df = pd.DataFrame({'col1': range(1, 11), 'col2': list('abcdefghij')})
df.head()
```

...and that the renv works by copying the r chunk below into test.Rmd:


```python

renv::activate()
library(ggplot2)
library(data.table)

for (i in 1:10){
    print(i)
}

df <- data.frame(c(col1 = 1:10, col2 = letters[1:10]))
setDT(df)

```

The R extension is recommended when working with R in VS Code:

<img src="../../images/02_R_extension.png" width=500> 

For good measure, we can also check that nextflow is present in the docker container with:


```python
nextflow -v
#nextflow version 24.10.6.5937
```

<img src="../../images/lightbulb.png" width=55> 

Remember, what happens in the container, stays in the container.. 

Any code you write in here or extensions you add will be wiped once the container is stopped (ideal for a practice environment). If you need some files to persist, they must be on the mounted volume. You have now learned how to use a docker image/ container. In this class, we have used it interactively - later, we will see how docker images integrate into bioinformatic workflows with Nextflow.

## Version Control

### Git
Git is a version control system that helps you track changes in your code and manage different versions of a project. By tracking your code, you can go back to earlier versions and see what changes were made between versions. 


<img src="../../images/02_git_branching_diagram.png"> 

[Image Credit](https://gist.github.com/bryanbraun/8c93e154a93a08794291df1fcdce6918#file-git-branching-diagram-md)

The active working version of your code is typically called the "main" branch. You can also have development branches where you make changes without affecting your main branch. When you're happy with your code you can then merge your changes with your main branch - implementing the changes you've made in the development branch into your main branch. 

When you have a version of your code that is working and you want to easily refer back to it, you can label the version of the code, also known as "tagging a release". 

You can think of each circle as checkpoints where you've saved your code and Git allows you to easily reference and work back from earlier versions at any time. This is handy if you accidentally introduce a bug into your code, you can go back and work off an earlier version when you know your code was working. 


GitHub is a website where you can store your Git repositories, making it easy to share and collaborate on projects with others. It allows multiple users to work on the same code simultaneously and provides access to a vast library of open-source projects created by developers around the world.

Git is included in our virtual environment, so all git commands should work out of the box when you have it activated. You can download [Git](https://git-scm.com/downloads) if you want to have it in your base environment. If you are using a Mac, git is included in the XCode developer tools. 

In BCHM5420, we will primarily walk through using git commands via the ***CLI***, but other options are available:

1. VS Code has built-in Git support, so you can track your changes and upload your changes to GitHub right from the Source Control panel (the little branch icon on the sidebar). 
2. [GitHub.Dev Editor](https://docs.github.com/en/codespaces/the-githubdev-web-based-editor) On any github repository website, change the URL from "github.com" to "github.dev" and this will open a web editor interface similar to VS Code. 
3. [GitHub Desktop](https://github.com/apps/desktop) is a free app that makes it easy to manage your Git projects with a visual interface (***GUI***), rather than the command line.




### Semantic Versioning

When you look at the releases of a bioinformatic tool on GitHub, you may notice they usually follow a 3 number pattern like **v1.2.0**.

<img src="../../images/02_semantic_versioning_exception.png" width=550 height=200>
<div style="font-size: 12px">
(with some exceptions)
</div>

This 3 number system is called *semantic versioning* and takes the format **major.minor.patch**. The major number is incremented when a backwards incompatible change is implemented (ie. if you were to use the new version of the same tool in the same way as you used the old version, it would fail) or a tool leaves the initial development phase - all other numbers start again from 0 when the major is increased. The minor number is incremented when a new feature is added. The patch number is bumped for small bug fixes. 

What would be the next release version for a bioinformatic tool that changed the input types required? It used to be v1.3.5. 


It would be v2.0.0, because if a user of the tool downloads the new version and enters the same type of inputs they are used to it will not work! Therefore, the major patch number is incremented and the minor & patch numbers reset to 0. 


## GitHub Account & Repository Set Up

<img src="../../images/laptop.png" width=55> 

Next, we will set up a remote repository ("repo") on GitHub to deposit your code and track your bioinformatic research project. Keep in mind that this repo can become public at the click of a button and that Git history tells all so it is important not to share private information like passwords or absolute paths on your computer when publishing to GitHub. 

You will want to [create a GitHub account](https://github.com/signup) if you don't have one already. If you want to use this account only for this class, you can use your uleth email and any user name (just identify your account to us in class). However, your class project is a great portfolio item to add to your resume so take that into consideration when choosing your account details. 


**SSH Keys**

Once you have your account and your virtual environment activated, you'll want to "clone your repo" to your computer. This means creating a copy of a repository from Github (the remote server) to your local computer.

***A quick aside to explain the jargon:*** in this context, the remote is the version of the repository hosted on GitHub. The local is the copy you now have on your own device, where you can edit and work offline. When you're working on Github, the repo on the website is the local copy and the remote copy is the version on your computer. 

Two options for cloning your repo are via HTTPS (which requires you to enter your username and password every time you connect to Github) or via SSH (which uses an SSH key for secure access). SSH is generally more convenient and secure for frequent interactions with GitHub.

<img src="../../images/02_git_ssh_clone.png" width=550 height=200>
<div style="font-size: 12px">

</div>


SSH keys are a secure way to connect your computer to GitHub without typing your username and password every time. You keep the private key on your computer, and GitHub stores the matching public key. Once we have it set up, you can push and pull your code between your computer and Github with a simple command on the terminal.

We will now walk through setting up your SSH keys so that you can add your key to your Github account.


To check if you already have an existing ssh key, enter the following command in your terminal:



```python
ls -al ~/.ssh
```

If you already have one created, you've probably done this before and know what you're doing. 

If you receive an error that ~/.ssh doesn't exist, you're on the right track to creating your key for the first time. 

**Creating your SSH Key**

This [tutorial](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) provides detailed info on generating your keys if you'd like more information than what's available in this notebook. The following steps were taken from this resource. 




```python
ssh-keygen -t ed25519 -C "your_email@example.com"
```



If your system is old and doesn't use ed25519 , you can create an rsa key.






```python
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

**The following will print out from your terminal** if you are using Mac. If you are using Windows or another Linux terminal, the /Users/your.name path may differ but the rest should remain the same


```python
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/your.name/.ssh/id_ed25519):
```

Type `/Users/your.name/.ssh/id_ed25519` and press enter. (replace your.name with your account name )

It will then ask you to enter a passphrase. If you want to remember this passphrase, then enter this here, otherwise, just press "Enter" to have no passphrase.

**The following should print out from the terminal:**



```python
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/your.name/.ssh/id_ed25519
Your public key has been saved in /Users/your.name/.ssh/id_ed25519.pub
```


You can now view your public key by entering the following in your terminal.


```python
cat /Users/your.name/.ssh/id_ed25519.pub
```

Your key should print out and look something like below:


```python
ssh-ed25519 <a bunch of random numbers and letters> <your email>
```

Copy this public key and go onto your github page to [connect the SSH Key to your account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

1. In the upper-right corner of any page on GitHub, click your profile photo, then click  Settings.
2. In the "Access" section of the sidebar, click  SSH and GPG keys.
3. Click New SSH key or Add SSH key.
4. In the "Title" field, add a descriptive label for the new key. For example, if you're using a personal laptop, you might call this key "Personal laptop".
5. Select "Authentication Key"
6. In the "Key" field, paste your public key.
7. Click Add SSH key.



<img src="../../images/02_you_did_it.gif"  >
<div style="font-size: 12px">
Congratulations! You've now set up your SSH Key!
</div>


You should now be able to clone your repo to your computer.

### Git basics

First, let's make a new repository on Github.


1. Click on your profile icon in the top right hand corner on your Github website, then select "Your repositories", and then select "New" (the green button in the top right corner)

<img src="../../images/02_create_repo.png" width = 550>


2. Select the name of your repository, and whether you want this repo to be public or private. You can change these details at any time! Select to add a README.md so that you can add information about your repo.

<img src="../../images/02_new_repo_details.png">

3. Click the green code button, select SSH, and copy the url to clone your repo

<img src="../../images/02_clone_repo.png">



4. Let's make a directory in your home directory to clone your repo into  


```python
mkdir -p ~/BCHM5420/02_set_up_dev_env/
cd ~/BCHM5420/02_set_up_dev_env/
```


<img src="../../images/lightbulb.png" width=55> 

- `mkdir` is a command to make a directory (or a folder) on your computer
- `~` refers to your home directory, ex. for Mac this is /Users/your.name 
- `-p` is a "flag" or "parameter" which will make the a parent folder if it doesn't exist. For example, if you didn't use the `-p` flag, and you didn't have a folder BCHM5420 that exists within your home directory (~), the command would fail.
- `cd` is a command to change directories. In this case we are changing directories to work within ~/BCHM5420/02_set_up_dev_env/ (extra tip: use `pwd` to determine your current working directory on the command line)


5. Now let's clone your repo





```python
git clone <paste url you copied in Step 3>
```

The following example goes through cloning our class repository but you should do this on your repository otherwise you won't be able to "git push" your changes to the Github website due to permissions.


If this is your first time connecting to github, you may see this

<img src="../../images/02_git_clone_known_hosts_auth.png" width = 550>
<div style="font-size: 12px">
You can enter yes
</div>


<img src="../../images/02_git_clone_success.png" >
<div style="font-size: 12px">
This is what you should see if your SSH key was set up successfully
</div>


If you get an error, there may have been issue setting up your SSH key.

<img src="../../images/02_git_clone_failure.png" width = 500>
<div style="font-size: 12px">
Uh oh! Dont worry, we can troubleshoot in class!
</div>


Let's assume everything went smoothly! Now we've got our repo. Let's change directories to work within the repository and check the status.



```python
cd <the name of your repo>
git status
```

<img src="../../images/02_git_status.png" >
<div style="font-size: 12px">
You're on the main branch and your local repo is the same as the local version!
</div>


Now, if it's just you who is working on your project, you can just work from the main branch. However, it is best practice when working with others and on version controlled repositories to create a different branch from the main branch.


```python
git checkout -b <your_branch_name> 
```

<img src="../../images/lightbulb.png" width=55> 

- when you see these guys `< >`, such as `<your_branch_name>`, this means you fill in the value as applicable (so don't include the < > in the name)

For example you would enter


```python
git checkout -b taras_branch
```

<img src="../../images/02_git_branch.png" >
<div style="font-size: 12px">
You've now switched from the main branch to your new branch
</div>

Now you are on a separate version from the main branch and you can make any changes you'd like here without impacting the main branch that others may be working from. 

Let's make a new test file in this branch that we will add to the repo


```python
touch new_analysis.txt
```

Now let's check the status again to see what happened


```python
git status
```

<img src="../../images/02_git_new_file.png" >
<div style="font-size: 12px">
You've now created a new file but it has not yet been added to the repository and changes will not yet be tracked
</div>


Since we've added a new file, we need to add this file to the repo and check the status


```python
git add new_analysis.txt
git status
```

<img src="../../images/02_git_add.png" >
<div style="font-size: 12px">
You've now added a new file to the repository but we need to commit the changes to lock it in
</div>

Before we commit our changes, we need to set up our username and email that will be associated with our commit on Github. You only have to do this step once.


```python
git config --global user.name "Your Name"
git config --global user.email you@example.com
```

Now let's commit our changes


```python
git commit -m <your descriptive message>
```

<img src="../../images/lightbulb.png" width=55> 

The `-m` parameter allows you to add a descriptive message for your commit. It is required for every commit you do and it is important to be descriptive so when you're tracking your changes, you'll know what you did at this step. 


In this case our message might be:


```python
git commit -m "added new analysis file"
```

<img src="../../images/02_git_commit.png" >
<div style="font-size: 12px">
You've now commited this file to the repository!
</div

Let's check the status one last time


```python
git status
```

<img src="../../images/02_git_status_after_commit.png" >
<div style="font-size: 12px">
All changes have been commited and there are no untracked changes!
</div

At this point we could merge our changes back to the main branch since everything we've done has been saved and tracked by git. 

To do this, we push our branch to the main branch


```python
git push
```

<img src="../../images/02_git_push.png" >
<div style="font-size: 12px">
One last step! We have to set the origin of this branch 
</div


```python
git push --set-upstream origin <your branch name>
```

<img src="../../images/02_git_push_success.png" >
<div style="font-size: 12px">
Success!
</div



<img src="../../images/02_git_website_after_pushing.png" width = 700 >
<div style="font-size: 12px">
On the repository website, we can see we've succesfully added our local changes to the remote Github Repository!
</div

<img src="../../images/02_git_website_branch.png" width = 700 >
<div style="font-size: 12px">
If we go to our branch on the site, we can see our new file was added!
</div

You're now set up with Github and all you need to make your commits! In later classes we will go over the process of merging your branch with the main branch and work in pairs to go over reviewing changes. 

In the mean time, you can make your own repositories and work from the main branch as you start out!

### Git Exercises

<img src="../../images/laptop.png" width=55> 


Let's walk through the levels of the [git-game](https://github.com/git-game/git-game) to get familiar with some more git commands.


Below are a few more resources to comfortable with Git and Github

- https://learngitbranching.js.org/ 
- https://ohmygit.org/

## Research Question Brainstorm

<img src="../../images/pencil.png" width=37>

Last class we discussed formulating a research question of interest that will be used as the basis for bioinformatic learnings in this course. Try to pick a specific, singular question to prevent scope creep, as even a seemingly simple question can quickly become multi-faceted! 
A good place to start is by referencing peer-reviewed literature. What are topics that interest you? What are some biologically-relevant issues appearing in the news or social media? How could you answer it with bioinformatic skills? Do you have access to enough data & metadata to answer this question? The research question you select will provide an opportunity to apply the bioinformatic principles and techniques covered throughout BCHM5420/NEUR5420, like interacting with your remote GitHub repository for your bioinformatic project through VS Code. 

## Recap

You should now have an environment set up that you can begin developing in that contains at least one ***virtual environment*** with necessary programming languages, an ***IDE***, ***version control*** mechanism, namely ***Git***, and know how best to ***name your files***. Hopefully, you also better understand the components of your computer that are involved in ***CLI*** we will be using. You have learned ways to make working from the command line easier, such as ***aliases***, setting your ***PATH*** environment variable, and ***ssh key pairs***.
