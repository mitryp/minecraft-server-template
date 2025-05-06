# Minecraft Server Docker Template

This is our Minecraft Server template for Docker Compose.

## Tech used

- The [Minecraft Server on Docker (Java Edition)](https://docker-minecraft-server.readthedocs.io/en/latest/) for hosting
  servers for arbitrary Minecraft versions with any mod loader and CurseForge mod pack;
- Optionally, [Playit.gg](https://playit.gg/) Docker agent for exposing the server to the internet.

## Quick start

To deploy the container with the default config, first copy the `.env.example` file into `.env`:

```shell
cp .env.example .env
```

Then, you can start the headless server without Playit:

```shell
docker compose up
```

Or, with Playit:

```shell
docker compose --profile public up 
```

Notes:

- You will need to provide your Docker agent key when using the `public` profile.
  Additionally, you will need to change the address and port of your Minecraft instance to `10.103.0.10:25565` in the Playit console.
  [More info.](#playitgg)
- To run a headless server, add `-d` to the Docker Compose command.

## Configuration

The services are configured through the `.env` file.
To start, copy the `.env.example` file into `.env` and open it using a text editor of your choice:

```shell
cp .env.example .env && nano .env
```

Here, the values can be changed based on one's needs.
The following sections cover the `.env` configuration variables.
Each section has a corresponding comment in the `.env.example` file.

### Container versions

Versions of the internal services' containers can be explicitly set:

```env
MC_CONTAINER=:java21
PLAYIT_CONTAINER=:0.15
```

Note that the values must start with a colon.

> These can be omitted, but you would probably want to set the
> matching `MC_CONTAINER` value for your target Minecraft version.

### Minecraft environment

These options determine the server version, type and more.

```env
MC_TYPE=AUTO_CURSEFORGE # required
MC_VERSION=1.21.1 # required
MC_MIN_MEMORY=4G # required
MC_MAX_MEMORY=10G # required
MC_SERVER_NAME=Minecraft server # default
MC_MOTD=A Minecraft server # default
MC_OPS="Player1\nPlayer2"
MC_WHITELIST="Player1\nPlayer2"
MC_ENFORCE_WHITELIST=TRUE # default
MC_SPAWN_PROTECTION=FALSE # default
MC_ONLINE_MODE=TRUE # default
```

Notes and links:

* You must set the variables marked as `required` for the container to run.
* Lists of values can be set as "value1\nvalue2\nvalue3" etc., as in the example above.
* [TYPE docs](https://docker-minecraft-server.readthedocs.io/en/latest/types-and-platforms/).
* [AUTO_CURSEFORGE docs](https://docker-minecraft-server.readthedocs.io/en/latest/types-and-platforms/mod-platforms/auto-curseforge/).
* Whitelist only works when `MC_ONLINE_MODE` is `TRUE`.
* The server configuration will not update on `.env` changes automatically after it was initially created.

### CurseForge mod pack options

This template uses CurseForge as main distribution source. It does not include options which
I didn't need yet while configuring my servers so the options here are limited.

If you use the `AUTO_CURSEFORGE` server type, you will need
to [get the CF API key](https://support.curseforge.com/en/support/solutions/articles/9000208346-about-the-curseforge-api-and-how-to-apply-for-a-key)
and set the `CF_API_KEY` and `CF_PAGE_URL` variables.

```env
CF_API_KEY="your-curseforge-key"
CF_PAGE_URL="https://www.curseforge.com/minecraft/modpacks/your-modpack/files/fileId"
CF_EXCLUDE_MODS="id1\nid2"
```

If any mods break the server loading due to being incorrectly marked as server-side,
they can
be [excluded](https://docker-minecraft-server.readthedocs.io/en/latest/types-and-platforms/mod-platforms/auto-curseforge/#exclude-client-mods)
by providing the `CF_EXCLUDE_MODS` variable as a list of mod IDs.

### Playit.gg

If you plan to expose the server to the internet using [playit.gg](https://playit.gg/),
you will need to set the PLAYIT_SECRET_KEY to your private Docker agent key.

```env
PLAYIT_SECRET_KEY="your-playit-gg-docker-deployment-key"
```

The agent itself can then be configured through the Playit web interface.

### RCON commands

This template currently exposes the following RCON commands (default values are shown):

```env
RCON_CMDS_STARTUP=gamerule doFireTick false
RCON_CMDS_FIRST_CONNECT=gamerule doFireTick true
RCON_CMDS_LAST_DISCONNECT=gamerule doFireTick false
```

Notes:

* These can also be lists of commands.
* The RCON commands set above ensures that the fire does not spread when there are no players online.
