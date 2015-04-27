# Opidio


Opidio is an Android application (and eventually a web app) made primarily for video consumption. The primary appeal is for content creators, as they will remain in full control of their material. Current video platforms like YouTube basicly says "Upload your video and we'll host it for 45% of the ad revenue". Opidio will try to make the 45% more competetive by providing the software to host their own videos (and therefore keep all ad revenues as well), but still being connected to one big site (or app).

More technically, the whole Opidio project will be split into three completely separated parts built in three different repositories:

- [`hub-server`](https://github.com/opidio/hub-server): The main server that aggregates multiple channels and provides an API for the client (Android APP). It contains the listing of all videos, but none of the video content itself. This server is also responsible for all social interactions.
- [`channel-server`](https://github.com/opidio/channel-server): The channel server hosts the content, and is in control of showing ads and restricting the content if required. One chanel server could be registered to multiple hub servers.
- [`android-client`](https://github.com/opidio/android-client): The client will connect to a hub server which will provide video listings and all social functions. When it comes to watching the video, the client will connect directly to the channel server as instructed by the hub server.

All code is split into the repos as linked above. This repo soley contains links to them in the form of submodules and some scripts and documentation.

## A note on Docker
Since this project is built in several smaller parts, I've used Docker to keep everything simple. 

## Starting everything manually

- `jwilder/nginx-proxy`: Reverse proxy listening on port 80
    ```bash
    docker run -d -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy
    ```

- `opidio/landing-page`: The landing page listening on a random port
   ```bash
   docker run -dP -e VIRTUAL_HOST=get.opid.io opidio/landing-page
   ```
   You can find out which port it's running on through `docker ps`, however nginx-proxy
   will pick it up automatically through docker and serving it at VIRTUAL_HOST.
