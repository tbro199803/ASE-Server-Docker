# Docker ARK Server

[中文文档](README-Zh.md)  

This is a Docker-based ARK: Survival Evolved game server that uses GE-Proton to run the Windows version of the ARK server, with ArkApi support.  
It includes the latest server files and ArkApi files at build time.

## Inspiration and Code Sources
- [Ark-Survival-Ascended-Server](https://github.com/Acekorneya/Ark-Survival-Ascended-Server)
- [Ark_Mod_Downloader_v2](https://github.com/CobraColin/Ark_Mod_Downloader_v2)

## Disclaimer
- *Compared to running directly on Windows, it will occupy more disk space
- Since we're using GE-Proton (based on Wine) to simulate Windows system APIs on Linux to run the Windows version of the game server, memory usage might be slightly higher
- Including the latest server files has its pros and cons. While it saves download time during initial deployment for multi-server setups, we may release a Docker image without server files in the future based on needs

## Known Issues
- When running the server with GE-Proton, server output information cannot be viewed in the container logs
- The Mods download/update process is not perfect

## Future Plans
- Test performance differences between running the server directly on Windows versus using GE-Proton on Linux, and whether UDP performance is better on Linux
- Would it be more suitable to use [umu-launcher](https://github.com/Open-Wine-Components/umu-launcher) to replace GE-Proton for running ASE server?
- Develop a server management system based on Go+Vue?

## Usage

### 1. Enter the docker directory
```bash
cd docker
```

### 2. Configure server.cfg file

The server parameters are not currently defined using environment variables in `docker-compose.yml` because modifying environment variables requires rebuilding the container.  
If you need to use Mods, you need to configure 'MODIDS' and enable the option to update Mods before starting 'UPDATE_MODS=true'.  
If you need to expose the RCON port, add the port mapping in `docker-compose.yml`.  
The `server.cfg` file in the project root directory contains all configurable environment variables, and changes can be applied by restarting the container without rebuilding:

```ini
PORT=7777 # Game client port
QUERYPORT=27015 # Query port for Steam's server browser
MAP=TheIsland # Map Name
MAX_PLAYERS=70 # Maximum players
UPDATE_SERVER=false # Update server before starting, set to true to enable
UPDATE_MODS=false # Update Mods before starting, set to true to enable
MODIDS="1,2,3" # Mods list, comma separated
SERVER_ARGS="-NoBattlEye -servergamelog -structurememopts -UseStructureStasisGrid -SecureSendArKPayload -UseItemDupeCheck -UseSecureSpawnRules -nosteamclient -game -server -log -MinimumTimeBetweenInventoryRetrieval=3600 -newsaveformat -usestore" # Server startup arguments
```

Simply edit the values in the `server.cfg` file and restart the container to apply the new configuration.

### 3. Available Maps

- TheIsland
- TheCenter
- ScorchedEarth_P
- Ragnarok
- Aberration_P
- Extinction
- Valguero_P
- Genesis
- CrystalIsles
- Genesis2
- LostIsland

### 4. Data Persistence

To save game data, it is recommended to mount the following volumes:

| Local Path | Container Path | Description |
|------------|----------------|-------------|
| ./server.cfg | /home/steam/server.cfg | *Required, configuration file for server startup parameters |
| ./Saved | /home/steam/arkserver/ShooterGame/Saved | Server save files, including Configs, Logs, SavedArks |
| ./Plugins | /home/steam/arkserver/ShooterGame/Binaries/Win64/ArkApi/Plugins | ArkApi plugin files location |
| ./ArkApiLogs | /home/steam/arkserver/ShooterGame/Binaries/Win64/logs | ArkApi log files |

### 5. Using Docker Compose

The project includes a `docker-compose.yml` file and a `server.cfg` file.  
Using Docker Compose makes it easier to manage the container:

```bash
# Build and start the container
docker-compose up -d

# View logs
docker-compose logs -f

# Stop the container
docker-compose down
```
