
# Home Media Server (HMS)
HMS is a collection of applications scripted into a Docker compose and ENV file for easy deployment and requires minimal configuration. I prefer to run Plex outside of Docker, if you plan to run Plex or another media server that will need to be installed manually. HMS comprises of the following applications:

* Portainer 
* Watchtower 
* Transmission VPN
* Radarr
* Sonarr
* Jackett

## Getting Started

These instructions will get you a copy of HMS up and running on your local machine. Please read the installation and configuration guide below. 

### Prerequisites

To use this script the following pre-requisites must be met:

* Docker
* Docker Compose
* A VPN provider such as IPVanish (See [haugene/transmission-openvpn] (https://github.com/haugene/docker-transmission-openvpn) for a list of supported providers.)

### Docker and post installation
The steps below are for installation on a Linux system, Windows and MacOS should be mostly the same.

Install docker from: [docker.com](http://docker.com) or using your favourite package  manager.

Create a Docker group on your system:
```
sudo groupadd docker
```

Add your user (that's you) to the Docker group
```
sudo usermod -aG docker $USER
```

#### Get PUID and PGID 
Get the PUID and PGID of your user account

PUID
```
id -u $USER 
```

GUID
```
id -g $USER
```

Make a note of the PUID and GUID those will be needed later.

### Installation
1. Download or clone this repository.
2. Edit the ENV file:

#### ENV file
```
USERDIR=(your docker user directory - from install)
SHARDIR=(where your downloads and media will go)
PUID=$PUID (from Docker post installation) 
PGID=$GUID (from Docker post installation)
TZ=/etc/localtime:/etc/localtime:ro
OPENVPN_PROVIDER=(VPN Provider)
OPENVPN_USERNAME=(VPN Provider username)
OPENVPN_PASSWORD=(VPN Provider password)
TRANSMISSION_RPC_USERNAME=(create a Transmission username)
TRANSMISSION_RPC_PASSWORD=(create a Transmission password)
```

3. Edit the compose file (optional)

#### COMPOSE file
You can change the default location of downloads and media in the compose file itself - just make sure the Downloads directory is consistent between Transmission, Radarr and Sonarr for renaming and moving files.

4. Create the directory structure below:

* ($USERDIR)/docker
* ($SHARDIR)/Downloads
* ($SHARDIR)/HMS/Movies
* ($SHARDIR)/HMS/TV

5. Install the stack
```
docker-compose -f docker-compose.yml up -d
```

## Configuration

After the stack has been installed the following containers will have been created, Watchtower will run in the background. If you're installing HMS remotely change localhost in the links below to match the IP of the host you are installing HMS to.

Open Portainer, create a username and password, verify that all containers have been created and are running - the Transmission container may take a while!

| Application  | URL                   |
|--------------|-----------------------|
| Portainer    | http://localhost:9000 |
| Transmission | http://localhost:9001 |
| Radarr       | http://localhost:9002 |
| Sonarr       | http://localhost:9003 |
| Jackett      | http://localhost:9004 |


### Jackett Proxy configuration
Configure Jackett to use the Tiny Proxy over VPN:

* proxy URL: http://{host IP Address}
* proxy PORT: 8888

Add indexers and make sure to test them before configuring Sonarr and Radarr.

### Radarr configuration
Configure Radarr to use Transmission and Jackett

**Transmission:** Settings > Download Client > + > Transmission

```
Name: Transmission
Host: IP of host machine
Port: 9001
Username: set in ENV file
Password: set in ENV file

```

Test and save the connection.

**Indexers:** Settings > Indexers > + > Torznab

```
Name: ALL indexers (Jackett)
URL: http://{host IP address}:{jacket-port}/torznab/all/api
API KEY: {from jackett}
```

Test and save the indexer.

Make any changes for file moving and renaming in Settings > Media Management.

### Sonarr configuration
Configure Sonarr to use Transmission and Jackett

**Transmission:** Settings > Download Client > + > Transmission

```
Name: Transmission
Host: IP of host machine
Port: 9001
Username: set in ENV file
Password: set in ENV file

```

Test and save the connection.

**Indexers:** Settings > Indexers > + > Torznab

```
Name: ALL indexers (Jackett)
URL: http://{host IP address}:{jacket-port}/torznab/all/api
API KEY: {from jackett}
```

Test and save the indexer.

Make any changes for file moving and renaming in Settings > Media Management.

## Built With

### Docker Images
* [portainer/portainer] (https://hub.docker.com/r/portainer/portainer/) - Portainer
* [v2tec/watchtower] (https://hub.docker.com/r/v2tec/watchtower/) - Watchtower
* [haugene/transmission-openvpn] (https://github.com/haugene/docker-transmission-openvpn) - Transmission OpenVPN Tiny Proxy
* [linuxserver/radarr] (https://hub.docker.com/r/linuxserver/radarr/ - Radarr)
* [linuxserver/sonarr] (https://hub.docker.com/r/linuxserver/sonarr/) - Sonarr
* [linuxserver/jackett] (https://hub.docker.com/r/linuxserver/jackett/) - Jackett 

### Other
* [PurpleBooth/README-Template.md] (https://gist.github.com/PurpleBooth/109311bb0361f32d87a2#file-readm) - GitHub README template


## Authors
**Kris Dunn**  [GitHub: KrisDunn](https://github.com/KrisDunn)


