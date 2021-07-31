This section will cover things required to actually run Freqtrade / NFI , such as installation of dependencies , ways to deploy NFI on your system and sync it with this repo to receive updates as they arrive , general configurations for Freqtrade such as a basic blacklist used by the community , rate limit settings , things of that sort . NFI Specific configurations will be in another section

# Freqtrade Setup and Syncing with Private Repo

Depending upon your OS , Freqtrade installation can differ to quite an extent . There are primarily 2 ways to do it either installing natively to your OS or using Docker . If you are really constrained with the resources you have go the native route , but it is highly recommended to use the Docker Installation since majority of the users use it and most development will be catering to that user base , plus its easier to get everything else configured once you have Docker Setup . You can check the [Freqtrade docs](https://www.freqtrade.io/en/stable/installation/) for the other methods , we will be covering the docker method of things for the sake of keeping things simple. 

## Docker Installation
This is 

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
  vio-live:
    build:
      context: .
      dockerfile: "./docker/Dockerfile.custom"
    restart: unless-stopped
    container_name: vio-live
    volumes:
      - "./user_data:/freqtrade/user_data"
    ports:
      - "0.0.0.0:8083:8083"
    command: >
      trade
      --logfile /freqtrade/user_data/logs/vio_live.log
      --db-url sqlite:////freqtrade/user_data/vio_live.sqlite
      --config /freqtrade/user_data/vio_live.json --config /freqtrade/user_data/vio_live-PRIVATE.json
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

# General Configs

