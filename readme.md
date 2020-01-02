# Home Media Server (HMS)

HMS is a collection of applications scripted into a Docker compose and ENV file for easy deployment and requires minimal configuration. I prefer to run Plex outside of Docker so if you plan to run Plex or another media server, that will need to be installed manually. HMS comprises of the following applications:

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
* A VPN provider (See [haugene/transmission-openvpn] (https://github.com/haugene/docker-transmission-openvpn) for a list of supported providers.)

### Installing HMS
The steps below are for installation on a Linux system, Windows and MacOS may differ but should be mostly the same.

#### Docker post installation steps
You can skip this step if you already have a Docker group and user.

Create a Docker group
```
sudo groupadd docker
```

Add your user to the Docker group
```
sudo usermod -aG docker $USER
```

#### Get PUID and PGID 
Get the PUID and PGID of the Docker $USER:

PUID
```
id -u $USER 
```

GUID
```
id -g $USER
```

#### Installation
1. Download or clone this repository
2. Edit the ENV file:

ENV file
```
USERDIR=(your docker user directory)
SHARDIR=(where your downloads and media will go)
PUID=$PUID (from Docker post installation) 
PGID=$GUID (from Docker post installation)
TZ=/etc/localtime:/etc/localtime:ro
OPENVPN_PROVIDER=(VPN Provider)
OPENVPN_USERNAME=(VPN Provider username)
OPENVPN_PASSWORD=(VPN Provider password)
TRANSMISSION_RPC_USERNAME=
TRANSMISSION_RPC_PASSWORD=
```

3. Edit the compose file (optional)

COMPOSE file
You can change the default location of downloads and media in the compose file itself - just make sure the Downloads directory is the consistent between Transmission, Radarr and Sonarr for renaming and moving files.

4. Create the directory structure below:
($USERDIR)/docker
($SHARDIR)/Downloads
($SHARDIR)/HMS/Movies
($SHARDIR)/HMS/TV

5. Install the stack
```
docker-compose -f docker-compose.yml up -d
```

## Configuration

After the stack has been installed the following containers will have been created, Watchtower will run in the background.

Open Portainer, create a user and verify if all containers have been created and are running - the Transmission container make take a while!

| Application  | URL                   |
|--------------|-----------------------|
| Portainer    | http://localhost:9000 |
| Transmission | http://localhost:9001 |
| Radarr       | http://localhost:9002 |
| Sonarr       | http://localhost:9003 |
| Jackett      | http://localhost:9004 |


### Jackett Proxy configuration
Configure Jackett to use the Tiny Proxy over VPN:

* proxy URL: http://{IP Address}
* proxy PORT: 8888

Add indexers and make sure to test them before configuring Sonarr and Radarr.

### Radarr configuration
Configure Radarr to use Transmission and Jackett

Transmisson
Settings > Download Client > + > Transmission

```
Name: Transmission
Host: IP of host machine
Port: 9001
Username: set in ENV file
Password: set in ENV file

```

Test and save the connection.

Indexers
Settings > Indexers > + > Torznab

```
Name: ALL indexers (Jackett)
URL: https://xxx.xxx.xxx.xxxx:{jacket-port}/torznab/all/api
API KEY: {from jackett}
```

Test and save the indexer.

Make any changes for file moving and renaming in Settings > Media Management.

### Sonarr configuration
Configure Sonarr to use Transmission and Jackett

Transmisson
Settings > Download Client > + > Transmission

```
Name: Transmission
Host: IP of host machine
Port: 9001
Username: set in ENV file
Password: set in ENV file

```

Test and save the connection.

Indexers
Settings > Indexers > + > Torznab

```
Name: ALL indexers (Jackett)
URL: https://xxx.xxx.xxx.xxxx:{jacket-port}/torznab/all/api
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
**Kris Dunn** - *Docker Compose* - [KrisDunn](https://github.com/KrisDunn)

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

