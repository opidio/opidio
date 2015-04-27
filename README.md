# opidio
A simple way to start alla Opidio servers


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
