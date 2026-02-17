# Linux User Group Docker Workshop
**Welcome to the Linux User Group Docker Workshop!**   
Ask for help 
## 1. Setup
Before we get started you'll need to following tools installed on your system:  
(*Windows users: Get the AMD64 download of the following tools unless you know otherwise.*)
1. [Docker](https://www.docker.com/products/docker-desktop/) - For running our containers.
2. [git](https://git-scm.com/install/windows) - For copying this repository.
3. [Make](https://www.gnu.org/software/make/manual/make.html) - For running provided setup scripts

Windows Terminal Commands:
```
winget install Docker.DockerDesktop Git.Git GnuWin32.Make
```
Linux Commands (using Ubuntu's `apt` for example):
```
sudo apt install docker.io docker-compose-v2 git make
# We need to begin the docker daemon:
sudo systemctl start docker
```
Mac Commands:
```
brew install --cask docker
brew install git make
```
I also recommend that Windows users use [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) if they have it installed on their system. Otherwise you should use your Windows Terminal or Powershell **NO COMMAND PROMPT**.

Once you have all the above tools installed, **restart your terminal**.

-----------------------------------------

## 2. Clone this Repository
Now that we have all the above tools installed, we'll clone this repository onto your computer. "Cloning" just means it will make a copy of this Github repository on your computer, so find where you want the new folder to go.  
*Note to Windows users: I recommend outside of the OneDrive folder, it might try syncing all the files, which is unnecessary.*    
*Another note to Windows users: If you're on WSL, start by running `cd ~` to get to your WSL home.*  

Once you've found a place, run the following command:
```
git clone https://github.com/jonfrutan/DockerRadio.git
cd DockerRadio #This will move you inside the newly created folder.
```
You'll now be inside the `DockerRadio` folder. 
If you type the command `ls` you'll see the following:
```
Radio
README.md
Sample
Visualizer
```
Briefly:
- `Radio` is where we'll be managing our docker containers.
- `Website` contains the "radio tuner" to listen to your stations.
- `Sample` contains some (royalty-free) sample music to get started.  

`README.md` is just the file you're reading right now, so it can be ignored.

--------------

## 3. Starting Docker

Navigate into the `Radio` folder with the following command:
```
cd Radio
```
If you type `ls` you'll see:
- `docker-compose.yml` - This is a **Docker Compose** file. For an analogy - A music note is to a musical composition what a Docker container is to a Docker Compose. This file structures all the necessary containers in order to achieve our goal.
- `icecast.xml` - This is some boilerplate info to configure our icecast server. You can ignore this.
- `Makefile` - This is a wrapper tool I've made to help you expedite the station setting up process. If you type and run `make` you'll get a list of commands you can run from it.



--------------


## 4. Exploring Docker (Under the Hood)

Our Makefile acts as a helpful wrapper, but it's important to know the actual Docker commands running the show.

Make sure your terminal is inside the DockerRadio/Radio folder (where the docker-compose.yml file lives) and that your station is currently running. Try running these commands to see how Docker operates:
#### Check Running Containers
```
docker compose ps
```
**What you see**: A table displaying your active containers (icecast and liquidsoap), their current status, and the ports they are exposing to your local machine (like 8000:8000).
#### View Live Logs
```
docker compose logs -f liquidsoap
```
**What you see**: A live stream of the terminal output from the liquidsoap container. You will see it successfully connecting to Icecast or reading your playlist files. (Press `Ctrl+C` to exit the live log stream).

#### Enter inside a container
```
docker exec -it liquidsoap /bin/sh
```
**What you see**: Your terminal will look different. You are now inside of the  liquidsoap Docker container Linux environment. Try running some commands in here like `ls /music`. Type `exit` to leave the container.


#### Restarting a Specific Service
```
docker compose restart liquidsoap
```
**What you see**: Docker will cleanly shut down and restart just the Liquidsoap container without touching the Icecast server. This command is particularly helpful because you can run this to update all the songs that liquidsoap can see.

