# Native Installation

# NFI AutoUpdater

NFI autoupdater scripts collection so far:
- https://github.com/shanejones/nfi-auto-update (by @Shane B@7.10 BTC@7.9.2 KC@7.4.2): NFI auto update (last commit) + Telegram notification + FT manual reload [bash + py]
- https://github.com/krsh-off/nfi-tags-auto-update (by @mansad):  NFI auto update  (last tag/version) + Telegram notification + FT manual reload [bash]
- https://github.com/potpot59/nfi-updater (by @Frremu v7.10.1 BUSD): NFI auto update  (last version) with backtesting + Telegram notification + Git tracking + FT auto reload + FT auto update [bash]
- https://github.com/lobap/NostalgiaForInfinity_Update (by Lobaq): NFI auto update (last commit) + Telegram notification + FT auto reload + FT auto update [bash]

# Grafana

Basically with grafana u can make beautiful dashboards to see stats of multiple bots running in multiple servers from a single point instead of having to go through every single instance's frequi . Its pretty handy to have when u are using multiple bots

You can have this grafana thing set up either on your bot running machine or any other machine where u want the grafana stuff to be (i perosnally use a spare vps i have , but the thing aint cpu intensive so you should be fine with anything tbh)

First in the machine u want grafana , ofc you need docker it its not installed (refer to prerequisites/preliminary) .
For Debian folks here is my personal quick setup command
```
sudo apt install glances -y && sudo apt install git -y && sudo apt update && sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common -y && curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" && sudo apt update && apt-cache policy docker-ce && sudo apt install docker-ce -y && sudo systemctl status docker && sudo usermod -aG docker ${USER} && su - ${USER}
```

```
sudo curl -L https://github.com/docker/compose/releases/download/1.25.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose && docker-compose --version
```

Once you have it run this thing (thanks Hippocritical)
```
docker run --name grafana -d -p 3000:3000 -v /home/usar/sqliteDBs/:/etc/grafana/provisioning/datasources -v /home/usar/grafanaDashboards/:/etc/grafana/provisioning/dashboards/ -e "GF_INSTALL_PLUGINS=frser-sqlite-datasource" grafana/grafana
```
here replace usar with whatever the username of the account is and  if u want to change the ports change 3000:3000 to whatever value u want (change the first thing only tbh)

Now if you dont have port 3000 opened on your firewall you should prolly do that , if not ssh tunnel into the grafana running machine at least
then just go to IP:3000 in your browser and tada you should see the grafana login page . Login with credentials admin (both username and password) then u will be prompted to make a new password , and you will finally get into the main grafana page

## Setup a datasource
Now before you can save the actual thing, you will need to copy over a db from anyone of your instance to the `/home/<user>/sqliteDBs/` folder, just to get like a dummy db from one of ur running instances in there to get stuff started. WinSCP might be a handy tool for this, and you might need sudo permissions to move the file to the folder.
Now in grafana on the left side, open the "burger" menu and navigate to `connections` -> `Add new connection`. You get to see alist of data sources plugins. Install `SQLite`, if it's not already installed. Now Navigate to `Data sources` to add your new db that u moved over. To do this, type in the directory path _inside_ the container where the db will be, eg: `/etc/grafana/provisioning/datasources/live.sqlite` if `live.sqlite` is the db file you just moved. Now the data source should be accessable.

## Setup the dashboard
To setup the dashboard, go to the + button to the left pane, go to import , import json 
now for the json file (the actual dashboard ) you will need to find one or make your own one . Leonardvdj has one , dont know if he likes to share it , but thats what i use . get the json file and upload it .in CCE choose the data source u just entered

now you should be seeing the dashboard with the data beautifully displayed 

Now to make it sync real time , u can setup a cron job to run a scp command (with sshpass if needed) , to copy files and update the files in the DB folder regularly . Something like `sshpass "password" scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null user@IP:/home/user/freqtrade/ft_userdata/user_data/live.sqlite /home/user/sqliteDBs/live_bot1.sqlite`


# BackTest Combiner

# Binance WebHook Proxy for Freqtrade
