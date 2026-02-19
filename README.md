# Linux User Group Docker Workshop
**Welcome to the Linux User Group Docker Workshop!**   
*Please ask for help if you need it! You can also view the Troubleshooting section below.*  
## 1. Setup
Before we get started you'll need to following tools installed on your system:  
(*Windows users: Get the AMD64 download of the following tools unless you know otherwise.*)
1. [Docker](https://www.docker.com/products/docker-desktop/) - For running our containers.
2. [git](https://git-scm.com/install/windows) - For copying this repository.
3. [Make](https://www.gnu.org/software/make/manual/make.html) - For running provided setup scripts

Windows Terminal Commands:
(*Note for WSL Users: Install Docker Desktop on Windows, but open your WSL terminal and use the Linux apt commands below to install git and make."*)
```bash
winget install Docker.DockerDesktop Git.Git GnuWin32.Make
```
Linux Commands (using Ubuntu's `apt` for example):
```bash
sudo apt install docker.io docker-compose-v2 git make
# We need to begin the docker daemon:
sudo systemctl start docker
```
Mac Commands:
```bash
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
```bash
git clone https://github.com/jonfrutan/DockerRadio.git
cd DockerRadio #This will move you inside the newly created folder.
```
You'll now be inside the `DockerRadio` folder. 
If you type the command `ls` you'll see the following:
```
Radio
README.md
Sample
Website
```
Briefly:
- `Radio` is where we'll be managing our docker containers.
- `Website` contains the "radio tuner" to listen to your stations.
- `Sample` contains some (royalty-free) sample music to get started.  

`README.md` is just the file you're reading right now, so it can be ignored.

--------------

## 3. Starting Docker

Navigate into the `Radio` folder with the following command:
```bash
cd Radio
```
If you type `ls` you'll see:
- `Playlists` - This directory is where our radio station playlists will go. Each station will have it's own subfolder here for you to fill up with music.
- `docker-compose.yml` - This is a **Docker Compose** file. For an analogy - A music note is to a musical composition what a Docker container is to a Docker Compose. This file structures all the necessary containers in order to achieve our goal.
- `icecast.xml` - This is some boilerplate info to configure our **Icecast** server. You can ignore this.
- `Makefile` - This is a wrapper tool I've made to help you expedite the station setting up process. If you type and run `make` you'll get a list of commands you can run from it.

Run the following commands:
1. `make init` - This will pull some system info (Your OS and CPU architecture) to find the appropriate docker images and commands. This will be output and held in a `.env` file.
2. `make station` - This will create a new radio station. You'll be prompted to enter a name for it. Running this will create a new folder under Playlists. For starting out try the name 'LUG'. 
3. `make up` - This will initialize and create the two docker containers: one for **liquidsoap** and one for **Icecast**.

After you run these three commands, find the playlist folder for the new station like so (we'll assume we created a station "LUG"):
```bash
cd Playlists/LUG
```
This folder will be empty. I've provided some sample music for you to put in it to try it out though. Run the following command:
```bash
cp ../../../Sample/* .
```
Forgive the ugly command! Since the sample directory is all the way back up at the root we just need to tell the copy command where to look to find all the music. (*Note: You may also open your file explorer and drag-and-drop the files by hand. As long as your new playlist folder has the mp3 files in it.*)

Once you've done this, run the following:
```bash
cd ../..
docker compose restart liquidsoap
```
This will restart the **liquidsoap** container, so it can see the newly added music. Now open up `Website/radio.html` with a browser of your choice. You can also do this through the command line:  
**Mac:**
```zsh
open ../Website/radio.html
```
**Linux:**
```bash
xdg-open ../Website/radio.html
```
**Windows (PowerShell/Terminal):**
```bash
start ..\Website\radio.html
```
**Windows (WSL):**
```bash
explorer.exe ..\\Website\\radio.html
```

--------------


## 4. Exploring Docker (Under the Hood)

Our Makefile acts as a helpful wrapper, but it's important to know the actual Docker commands being used.

Make sure your terminal is inside the DockerRadio/Radio folder (where the docker-compose.yml file lives) and that your station is currently running. Try running these commands to see how Docker operates:
#### Check Running Containers
```bash
docker compose ps
```
**What you see**: A table displaying your active containers (**Icecast** and **liquidsoap**), their current status, and the ports they are exposing to your local machine (like 8000:8000).
#### View Live Logs
```bash
docker compose logs -f liquidsoap
```
**What you see**: A live stream of the terminal output from the **liquidsoap** container. You will see it successfully connecting to **Icecast** or reading your playlist files. (Press `Ctrl+C` to exit the live log stream).

#### Enter inside a container
```bash
docker exec -it liquidsoap /bin/sh
```
**What you see**: Your terminal will look different. You are now inside of the  **liquidsoap** Docker container Linux environment. Try running some commands in here like `ls /music`. Type `exit` to leave the container.


#### Restarting a Specific Service
```bash
docker compose restart liquidsoap
```
**What you see**: Docker will cleanly shut down and restart just the **liquidsoap** container without touching the **Icecast** server. This command is particularly helpful because you can run this to update all the songs that **liquidsoap** can see.

#### Monitor Container Resources
```bash
docker stats
```
**What you see**: A live dashboard showing you how much CPU, RAM, and bandwidth your containers are using. (`CTRL-C` to exit)

#### View Docker Networks
```bash
docker network ls
```
**What you see**: A list of the virtual networks Docker is currently managing. You should see `radio_net` listed here, which is the isolated bridge network our `Makefile` configured to allow Icecast and Liquidsoap to communicate

#### Start the Stack
```bash
docker compose up
```
**What you see**: Docker will start up all the containers, mount their volumes, and hook them up to a Docker network. **This is how you can begin your radio**. (You can also use `make up` as provided.)

#### Stop the Stack
```bash
docker compose down
```
**What you see**: Docker will cleanly shut down the containers and remove the radio_net virtual network. **This is how you should shut down your radio**. (You can also use `make down` as provided.)

Now if you reload your `radio.html` webpage, you should see your new radio stations being picked up.

## Conclusion
If you're able to see your radio stations listed on the `radio.html` you've successfully used Docker to deploy your own radio stations! Liquidsoap can render almost any audio file, so go ahead and drop anything you've got into your playlist folders. 

### Want more?
Try experimenting with `radio.liq` and `icecast.xml` to modify your broadcasts. Try implementing [crossfading](https://www.liquidsoap.info/doc-dev/crossfade.html) into your audio broadcasts. You can also try changing the streaming codec of Icecast into something more universal like AAC or lightweight like OGG.

## Troubleshooting

**Linux - Error: "failed to create network... bridge"**  
If you are on native Linux and get an error relating to the bridge network or missing kernel modules when running `make up`, your Linux kernel might not have the bridge module loaded. Run the following to enable it and restart Docker:
```bash
sudo modprobe br_netfilter
sudo systemctl restart docker
```

**Error: "port is already allocated" or "bind: address already in use"**  
This means another application on your computer is already using port 8000 (which Icecast needs). You will need to stop that application, or edit the docker-compose.yml file to map Icecast to a different local port (e.g., changing "8000:8000" to "8080:8000").

**Error: "Cannot connect to the Docker daemon"**  
This means your Docker Engine isn't running in the background.

- **Mac/Windows**: Open the "Docker Desktop" graphical application and wait for the engine icon to turn green.
- **Linux**: Run `sudo systemctl start docker`.

**Linux - Error: "Permission denied" when running Docker commands**  
If you are a native Linux user, your account might not be in the docker user group. Add `sudo` to the beginning of your docker commands (e.g., `sudo docker compose ps`).