Freqtrade on Local System

Depending upon your OS the installation can differ quite a bit , but since I'm using docker most of these steps should remain the same except ofc setting up docker that is .

Docker Installation 
Freqtrade has a docker image which makes it quite easy to install across different OS , but as I said earlier the tough part here is getting docker 

For  Linux :
For Linux things are pretty straight forward

Follow this guide to get docker installed : [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-10](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-10) , till step 4 , where you run hello world , the rest is quite unnecessary 

Then configure docker to start on boot :
`sudo systemctl enable docker.service`
`sudo systemctl enable containerd.service`

Now to Install docker-compose follow step 1 in : [https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-debian-10](https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-debian-10)

### For Windows :

For Windows things get a little complex since we need Hyper-V and WSL for docker . In my opinion windows has the hardest time to get get things configured 

First check if hyper-v is supported by your system

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3bc44937-34c2-4aaa-8a4f-7ae74f293d13/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210729%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210729T200709Z&X-Amz-Expires=86400&X-Amz-Signature=dfabe6ed3ae35224e2445fca940c016bbb2276fc5c6fa68ae10e6e1831c4e2e5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

If you see a No after the Virtualization Enabled In Firmware setting, you have to go into your BIOS (or UEFI firmware) settings and turn on support for virtualization , this guide might help you with that [https://www.virtualmetric.com/blog/how-to-enable-hardware-virtualization](https://www.virtualmetric.com/blog/how-to-enable-hardware-virtualization)
Alternatively you could just watch a YouTube video of a walkthrough for your systems BIOS settings

You can verify if the virtualization got enabled by using `systeminfo` command again

Now to enable Hyper-V : [https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#:~:text=DISM%20Technical%20Reference.-,Enable%20the%20Hyper%2DV%20role%20through%20Settings,Hyper%2DV%20and%20click%20OK) 

If you are on Windows Home use this : [https://www.itechtics.com/enable-hyper-v-windows-10-home/](https://www.itechtics.com/enable-hyper-v-windows-10-home/)

For WSL and Docker Installation : [https://andrewlock.net/installing-docker-desktop-for-windows/](https://andrewlock.net/installing-docker-desktop-for-windows/)
( Docker Desktop comes with docker and docker compose ) . Also you can just skip the optional stuff , just follow along till the logout and signin part

### For Mac :

I got no idea on how things are on the Mac , my friends say its easier than windows But yeah I never tested it first hand

(INSTALLATION WITHOUT DOCKER ON LINUX) (look into this later)

The Freqtrade docs is a good place to start if you want to properly lean how things are : [https://www.freqtrade.io/en/stable/docker_quickstart/](https://www.freqtrade.io/en/stable/docker_quickstart/)

But if you prefer videos DevBootstrap on YouTube does a really good job in getting you started and get you used to the whole system , he has 3 videos covering it [https://youtube.com/playlist?list=PL0rsv4zz8BfYu-8CrOjYAEdwQY2hFBXmQ](https://youtube.com/playlist?list=PL0rsv4zz8BfYu-8CrOjYAEdwQY2hFBXmQ)

(I actually recommend that you watch the video also , its really good)