# WebCenter Content on Docker

## Overview

This repository contains a [Dockerfile](images/wcc/Dockerfile) plus a [Docker-Compose](docker-compose.yml) file for running [Oracle WebCenter Content](https://www.oracle.com/technetwork/middleware/webcenter/content/overview/index.html) 12.2.1.4 on [Oracle WebLogic Server](https://www.oracle.com/middleware/technologies/weblogic.html) 12.2.1.4 using [Oracle Database](https://www.oracle.com/database/technologies/) Enterprise Edition 12.2.0.1.

## Requirements

### Software

- [Docker 18.09.0 or greater](https://docs.docker.com/install/#supported-platforms)
  - Version 18.09.0 or greater is required to use the `chown` [flag when copying files](https://github.com/moby/moby/pull/35521)
  - The [docker-compose.yml](docker-compose.yml) is using version `3.7` which [requires at 18.06.0 or greater](https://docs.docker.com/compose/compose-file/#compose-and-docker-compatibility-matrix)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  - This is used to [clone the repository](#clone-this-repository) (and comes pre-installed on most Linux OSes)

### Accounts

- [Oracle account](https://profile.oracle.com/myprofile/account/create-account.jspx) (to use pre-built images with binaries)
- You will need [sudo / superuser rights](https://en.wikipedia.org/wiki/Sudo) to the machine in order to install Docker and Docker Compose

### Disk Space

- [WebCenter Content zip binary](#download-webCenter-content-zip-binary) = 1.6 GiB
- image `middleware/fmw-infrastructure:12.2.1.4-200316` = 2.3 GiB
- image `database/enterprise:12.2.0.1` = 3.4 GiB
- image `oracle/wccontent:12.2.1.4` = 5.6 GiB

## Image and Container Information

### URLs

- WebLogic Server Administration Console: <http://localhost:7001/console/>
- WebCenter Content: <http://localhost:16200/cs/>

Credentials can be found in the [wcc.env file](wcc.env).

### Image Name

`oracle/wccontent:12.2.1.4`

This is defined in the [docker-compose.yml](docker-compose.yml).

### Container Names and Services

- Database: `oracle-db`
- WebCenter Content and WebLogic Server: `oracle-wcc`

These are defined in the [docker-compose.yml](docker-compose.yml).

### View Container Logs

- Database: `docker logs -f oracle-db`
- WebCenter Content and WebLogic Server: `docker logs -f oracle-wcc`

`oracle-db` and `oracle-wcc` are the [container names](#container-names-and-services).

## Build

### Clone this Repository

1. Navigate to a local directory where you want to clone this repository
2. Run this command in a terminal: `git clone https://github.com/jhult/wcc-on-docker`

Alternatively, you can download the entire repository as a zip file by [clicking here](https://github.com/jhult/wcc-on-docker/archive/master.zip).

### Login to the Oracle Container Registry

1. Navigate to the [Oracle Container Registry](https://container-registry.oracle.com)
2. Sign In using your Oracle account
3. Navigate to [*Middleware*](https://container-registry.oracle.com/pls/apex/f?p=113:1:13639930739021::NO:1:P1_BUSINESS_AREA:2) > *fmw-infrastructure*
4. Click Continue to agree and accept the Oracle terms and restrictions
5. Navigate to [*Database*](https://container-registry.oracle.com/pls/apex/f?p=113:1:12598430189688::NO:1:P1_BUSINESS_AREA:3) > *enterprise*
6. Click Continue to agree and accept the Oracle terms and restrictions
7. Run this command in a terminal and enter your your Oracle account credentials: `docker login container-registry.oracle.com`

### Download WebCenter Content zip binary

1. Download the binary zip from [here](https://www.oracle.com/middleware/technologies/webcenter-content-download.html) and place in this location: `[CLONED_REPOSITORY_DIRECTORY]/images/wcc/V983399-01.zip`

### Build Images

1. Navigate to your local cloned repository directory
2. Run this command in a terminal: `docker-compose build`
3. You should eventually see a message indicating success:
    > Successfully built 7a59abdf961f  
    > Successfully tagged oracle/wccontent:12.2.1.4

`oracle/wccontent:12.2.1.4` is the [image name](#image-name)

## First Run

1. Run `docker-compose up` to start [the containers](#container-names-and-services)

2. You may see an error as follows:

    > oracle-wcc    | wait-for: oracle-db:1521 is available after 66 seconds  
    > oracle-wcc    |  
    > oracle-wcc    | RCU Logfile: /tmp/RCU2019-09-26_18-43_870583981/logs/rcu.log
    > oracle-wcc    |  
    > oracle-wcc    | Enter the database password(User:sys):  
    > oracle-wcc    |  
    > oracle-wcc    | Processing command line ....  
    > oracle-wcc    | Invalid SID or Service name.  
    > oracle-wcc    | Enter valid SID or Service name.  
    > oracle-wcc    |  
    > oracle-wcc    | ERROR - RCU-6090 Connection step validation failed.  
    > oracle-wcc    | CAUSE - RCU-6090 Skipping main operation: failed to connect to database because database details were missing or invalid.  
    > oracle-wcc    | ACTION - RCU-6090 Provide correct database details and try again.  
    > oracle-wcc exited with code 1  

    If so, do the following:
    - Click CTRL + Z to background the database process
    - [Start](#start) just the WebCenter Content container

3. Wait several minutes for WebCenter Content to fully start; you can check the status by [viewing the container logs](#view-container-logs)
4. Finish the Post-Installation Configuration
5. [Stop](#stop) and [start](#start) just the WebCenter Content container

## Start / Stop

1. These commands should be run from the directory where the [docker-compose.yml](docker-compose.yml) exists. Alternatively, you can pass the option `-f` and specify the location of the docker-compose.yml file. Example: `docker-compose -f /tmp/wcc-on-docker/docker-compose.yml start`
2. `oracle-db` and `oracle-wcc` are the [container names](#container-names-and-services)

### Start

- Start the entire stack: `docker-compose start`
- Start just WebCenter Content (Oracle Database must already be started): `docker-compose start oracle-wcc`

### Stop

- Stop entire stack: `docker-compose stop`
- Stop just WebCenter Content: `docker-compose stop oracle-wcc`

There is a 5 minute grace period to stop the both containers.

## Support

### Oracle Support

Oracle supports Oracle FMW Infrastructure in certified Docker containers. Please read [Support Information for Oracle WebLogic Server and Oracle Fusion Middleware Running in Docker Containers (Doc ID 2017945.1)](https://support.oracle.com/CSP/main/article?cmd=show&type=NOT&id=2017945.1).

**Oracle does not yet support running WebCenter Content in a Docker container.**

For additional details on the most current FMW Infrastructure
supported configurations, please refer to the [Oracle Fusion Middleware Supported System Configurations](https://www.oracle.com/technetwork/middleware/ias/downloads/fusion-certification-100350.html).

### Bugs and Feedback

For support, bug reporting and feedback about the [provided Dockerfile](images/wcc/Dockerfile), please open an [issue on GitHub](https://github.com/oracle/docker-images/issues).

### Community Support

If you need general support with running containers on Oracle Linux, please visit the [OTN Community Container Space](https://community.oracle.com/community/server_&_storage_systems/containers).
