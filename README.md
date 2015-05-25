# Opidio


Opidio is an Android application (and eventually a web app) made primarily for video consumption. The primary appeal is for content creators, as they will remain in full control of their material. Current video platforms like YouTube basicly says "Upload your video and we'll host it for 45% of the ad revenue". Opidio will try to make the 45% more competetive by providing the software to host their own videos (and therefore keep all ad revenues as well), but still being connected to one big site (or app).

More technically, the whole Opidio project will be split into three completely separated parts built in three different repositories:

- [`hub-server`](https://github.com/opidio/hub-server): The main server that aggregates multiple channels and provides an API for the client (Android app). It contains the listing of all videos, but none of the video content itself. This server is also responsible for all social interactions.
- [`channel-server`](https://github.com/opidio/channel-server): The channel server hosts the content, and is in control of showing ads and restricting the content if required. One chanel server could be registered to multiple hub servers.
- [`android-client`](https://github.com/opidio/android-client): The client will connect to a hub server which will provide video listings and all social functions. When it comes to watching the video, the client will connect directly to the channel server as instructed by the hub server.

All code is split into the repos as linked above. This repo soley contains documentation regarding the project as a whole.

## A note on Docker
Since this project is built in several smaller parts, I've used Docker to keep everything simple. Docker will run everything in a container and will install all dependencies for you. If you do not wich to use Docker, you can see the `Dockerfile` as a detailed list of all dependencies required to run each individual server.

### Installing `docker` and `docker-compose`
`docker` is the main program maintaining the containers (the one reading `Dockerfile`s). `docker-compose` is descriptions of containers and containers they depend on (for example the hub server depends on a database container, so `docker-compose` will start a Postgre database before starting the hub server.

#### Installing `docker`
Most Linux distros has prebuilt binaries. Using arch or debian for example:
```bash
pacman -S docker # Arch
sudo apt-get update && sudo apt-get install docker.io # Debian
```
for complete instructions see [docs.docker.com/installation](https://docs.docker.com/installation/).

#### Installing `docker-compose`
Compose can be either installad as a python package or as a simple self-contained binary. See
[docs.docker.com/compose/install](http://docs.docker.com/compose/install/) for complete instructions.

## Running the servers
This is a complete guide of how I set up all servers on a VPS (including a reverse proxy). It's possible to skip all except hub-server.
### Reverse proxy
First up I'll get a reverse proxy that each docker container will plug into. For this I'm using the simple [nginx-proxy](https://github.com/jwilder/nginx-proxy)
```bash
docker run --restart=always -d -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy
```
### Web client ([opidio/web-client](https://github.com/opidio/web-client))
The webclient is a placeholder app for the startpage at [opid.io](http://opid.io). Currently it's nothing but a static html page.
```bash
# Grab and build
git clone https://github.com/opidio/web-client.git && cd web-client
docker build -t web-client .
# Start it
docker run -d --restart=always -e VIRTUAL_HOST=opid.io web-client
```
### Landing page ([opidio/landing-page](https://github.com/opidio/landing-page))
The landing page with a signup form, hosted at [get.opid.io](http://get.opid.io)
```bash
# Grab and build
git clone https://github.com/opidio/landing-page.git && cd landing-page
docker build -t landing-page .
# Start it
docker run -d --restart=always -e VIRTUAL_HOST=get.opid.io landing-page
```
### `hub-server`

Currently only the `hub-server` is in a working state, and the `android-client` is still not connected to it.

Now that you have Docker installed, running the servers should be as easy as `git clone ... && docker-compose up`. See the individual projects READMEs for more details.
