This section will cover things required to actually run Freqtrade / NFI , such as installation of dependencies , ways to deploy NFI on your system and sync it with this repo to receive updates as they arrive , general configurations for Freqtrade such as a basic blacklist used by the community , rate limit settings , things of that sort . NFI Specific configurations will be in another section

# Freqtrade Setup and Syncing with Private Repo

Depending upon your OS , Freqtrade installation can differ to quite an extent . There are primarily 2 ways to do it either installing natively to your OS or using Docker . If you are really constrained with the resources you have go the native route , but it is highly recommended to use the Docker Installation since majority of the users use it and most development will be catering to that user base , plus its easier to get everything else configured once you have Docker Setup . You can check the [Freqtrade docs](https://www.freqtrade.io/en/stable/installation/) for the other methods , we will be covering the docker method of things for the sake of keeping things simple. 

## Docker Installation
This is also be going to vary a bit depending on your OS , but once you have docker installed things should be easy.

### Docker For Windows
For Windows Docker Installation carries a few prerequisites , first of all we need to check if Hyper-V is supported for your system.

To Check this run `systeminfo` in cmd
![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3bc44937-34c2-4aaa-8a4f-7ae74f293d13/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210802%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210802T134639Z&X-Amz-Expires=86400&X-Amz-Signature=4e1d4745b2548651e870f7b06abefb6de910efa5e8aa89dc81bf6582af479fcd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

If you see a No after the Virtualization Enabled In Firmware setting, you have to go into your BIOS (or UEFI firmware) settings and turn on support for virtualization , this guide might help you with that https://www.virtualmetric.com/blog/how-to-enable-hardware-virtualization
Alternatively you could just watch a YouTube video of a walkthrough for your systems BIOS settings (but you probably know this)

You can verify if the virtualization got enabled by using `systeminfo` command again

Now to enable Hyper-V : [https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#:~:text=DISM%20Technical%20Reference.-,Enable%20the%20Hyper%2DV%20role%20through%20Settings,Hyper%2DV%20and%20click%20OK) 

If you are on Windows Home use this : [https://www.itechtics.com/enable-hyper-v-windows-10-home/](https://www.itechtics.com/enable-hyper-v-windows-10-home/)

For WSL and Docker Installation : [https://andrewlock.net/installing-docker-desktop-for-windows/](https://andrewlock.net/installing-docker-desktop-for-windows/)
( Docker Desktop comes with docker and docker compose ) . Also you can just skip the optional stuff , just follow along till the logout and signin part


### Docker For Linux
Its pretty straight forward for Linux , since there is no virtualization involved.

Follow this guide to get docker installed : https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-10 , till step 4 , where you run hello world , the rest is quite unnecessary 

Then configure docker to start on boot :
```
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

Now to Install docker-compose follow step 1 in : https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-debian-10

### Docker For Mac
I got no idea on how things are on the Mac , my friends say its easier than windows But yeah I never tested it first hand . 
Would be great if someone else could fill this up :D


_**Now with that done you should be all set to install to Freqtrade next**_

## Freqtrade Installation For Docker
The Freqtrade Documentation has this part covered quite well https://www.freqtrade.io/en/stable/docker_quickstart/
I would suggest you follow this . Also dont bother much about what you choose for the configs , we will be replacing it with something fine tuned for NFI and give u suggestions on how to optimise it 

(if you want to get used to Freqtrade a bit before you get into using NFI , check this [playlist](https://www.youtube.com/playlist?list=PL0rsv4zz8BfYu-8CrOjYAEdwQY2hFBXmQ) )



# NFI Deployment

NFInext since v7.2 requires the installation of external dependencies (pandas_ta)

To Install this on docker , we need to make a few changes in the docker-compose.yml and make a new dockerfile.custom to build a new Image containing the now required dependencies

So first , add the build line to your docker-compose file
```
 build:
      context: .
      dockerfile: "./docker/Dockerfile.custom"
```
and remove the freqtrade image line

Your compose file should look something like this
```
---
version: '3'
services:
  freqtrade:
    build:
      context: .
      dockerfile: "./docker/Dockerfile.custom"
    restart: unless-stopped
    container_name: freqtrade
    volumes:
      - "./user_data:/freqtrade/user_data"
    ports:
      - "0.0.0.0:8080:8080"
    command: >
      trade
      --logfile /freqtrade/user_data/logs/freqtrade.log
      --db-url sqlite:////freqtrade/user_data/tradesv3.sqlite
      --config /freqtrade/user_data/config.json
```

Now we need to create the build instructions in dockerfile.custom , for this first make a directory `docker` and then inside it make a new file `Dockerfile.custom` . Inside it paste the following snippet
```
FROM freqtradeorg/freqtrade:develop

# Switch user to root if you must install something from apt
# Don't forget to switch the user back below!
USER root

# Dependencies
RUN pip install pandas-ta

#switching back to normal user
USER ftuser
```

After u make the Dockerfile.custom file , run `docker-compose build --no-cache` to build the new image with the required dependencies
Now to run the bot , its the usual `docker-compose up -d` 

# VPS Bot Hosting

# General Configs

