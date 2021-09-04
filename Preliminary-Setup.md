This section will cover things required to actually run Freqtrade / NFI , such as installation of dependencies , ways to deploy NFI on your system and sync it with this repo to receive updates as they arrive , general configurations for Freqtrade such as a basic blacklist used by the community , rate limit settings , things of that sort . NFI Specific configurations will be in another section

# Freqtrade Setup and Syncing with Private Repo

Depending upon your OS , Freqtrade installation can differ to quite an extent . There are primarily 2 ways to do it either installing natively to your OS or using Docker . If you are really constrained with the resources you have go the native route , but it is highly recommended to use the Docker Installation since majority of the users use it and most development will be catering to that user base , plus its easier to get everything else configured once you have Docker Setup . You can check the [Freqtrade docs](https://www.freqtrade.io/en/stable/installation/) for the other methods , we will be covering the docker method of things for the sake of keeping things simple. 

### Native Installation
If you go with a lower end machine and you dont have enough swap allocated , you might want to check out the Native Installation , since now docker seems to be struggling with a 1GB ram system . You can find more instructions [over here]()
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
FROM freqtradeorg/freqtrade:stable

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

Choosing a VPS is completely optional but highly recommended , since it gives u the least latency to the exchanegs servers and uninterrupted running of the bot compared to it running on your local machine such as a pi or your PC . So yeah if u dont mind it , highly encouraged to get one .

So regarding the choice of VPS , most NFI users here prefers Vultr due to its great performance and reasonable pricing , but for those who want the cheapest option there is maybe try to go for contabo

[Here is a referral link for Vultr](https://www.vultr.com/?ref=8904923-6G) , This gives you 100 USD of credits to use for 30 days in vultr

For Binance and Kucoin , Vultr tokyo high frequency plans seem to be the most optimal , so I suggest you for the same
(btw use these referrals to sign up for exchanges if possible to support iterativ : [Binance](https://accounts.binance.com/en/register?ref=37365811) , [Kucoin](https://www.kucoin.com/ucenter/signup?rcode=rJTLZ9K) , [Huobi]{https://www.huobi.com/en-us/topic/double-reward/?invite_code=ubpt2223}


Now Assuming u chose Vultr , here are the instructions to set things up fast
[this is a note i have written a while ago regarding VPS service providers and how to set the bot](https://www.notion.so/violetto/Setting-up-VPS-and-Syncing-via-Git-ee9174f9a383416990f16320eeb0d31a)
First choose a plan from the high frequency line of VPS , then choose Tokyo location and Debian 10 x64 , the rest fill as you want to and make your new VPS instance . 

Grab a ssh client (most os have a ssh client pre installed in them , go check terminal) , and then login to the vps as root 

Next since this is a fresh Linux system , you need a sudo user account created . For that follow [this](https://www.vultr.com/docs/create-a-sudo-user-on-debian-best-practices)

Maybe also allocate swap to the system https://www.vultr.com/docs/setup-swap-file-on-linux

The Rest on how to set up docker and docker-compose is above in the [linux installation[(https://www.vultr.com/docs/create-a-sudo-user-on-debian-best-practices) part

Next regarding how to add strategy file to the vps instance , there are multiple ways to do this 

Option 1 : Add File Manually to VPS folder
For this we will use GUI enabled SFTP client , I suggest something like WinSCP for windows users , for other OSs I am not that familiar
Now via WinSCP you can manually add files to the desired location in your vps and drag and drops stuff in there from your local machine .

Option 2 : Including the file download instruction in the docker build file
For this make you dockercustom file something like this
```
FROM freqtradeorg/freqtrade:stable

# grabing strategy from web
ADD https://raw.githubusercontent.com/iterativv/NostalgiaForInfinity/main/NostalgiaForInfinityNext.py /freqtrade/user_data/strategies/docker-grab/
VOLUME /freqtrade/user_data/strategies/docker-grab/

# Switch user to root if you must install something from apt
# Don't forget to switch the user back below!
USER root

# Dependencies
RUN pip install pandas-ta

#switching back to normal user
USER ftuser
```
Also make a empty folder docker-grab in strategies folder for docker

Now with this when you rebuild your docker image with `docker-compose build --no-cache` the latest available strategy from git will be downloaded along with dependencies and be binded to the image . Be sure add the docker-grab folder to the strategies directory in the docker file for this to work

Option 3  : Clone NFI git repo and symlink it to strategies folder
(i havent tried this but everyone other than me is using this on the NFI server , If someone reads this and knows how to do this please fill the thing here briefly or DM me ~Violtetto)

This is what is generally recommended by the community , and many auto strategy updaters have been written around this (these can be found in extra stuff / tips)

# General Configs

For optimal performance, suggested to use between 4 and 6 open trades, with unlimited stake.

A pairlist with 40 to 80 pairs. Volume pairlist works well.

Prefer stable coin (USDT, BUSD etc) pairs, instead of BTC or ETH pairs.

Highly recommended to blacklist leveraged tokens (*BULL, *BEAR, *UP, *DOWN etc).

Ensure that you don't override any variables in you config.json. Especially the timeframe (must be 5m).

* `use_sell_signal` must set to true (or not set at all).
* `sell_profit_only` must set to false (or not set at all).
* `ignore_roi_if_buy_signal` must set to true (or not set at all).


