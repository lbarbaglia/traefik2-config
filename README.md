# Traefik v2.3.2 configuration
This is the docker compose and configuration for an instance of Traefik v2.3.2

> You can find a quick draft for v1.7 configuration on [an older commit](https://github.com/lbarbaglia/traefik-config/tree/4f17260d657d069c485c4bffea6b3e23753f0a32)

## Features
- HTTP redirect to HTTPS
- Let's Encrypt auto-generated certificates via HTTP challenge

## Be careful as the Traefik dashboard is configured as *insecure*. You can comment [line 20](https://github.com/lbarbaglia/traefik-config/blob/5515a58233d8e41b61b1d40c3c800519a2d2c9ca/traefik.toml#L20) of traefic.toml to prevent this behavior

## Installation
1. Clone the repository  
2. You first have to create an empty "acme.json" file at the base of this folder. Then set the correct permissions.  
`touch acme.json & chmod 600 acme.json`  
> Let's Encrypt is requiring an acme.json file to store certificates. [Click here to know more](https://doc.traefik.io/traefik/https/acme/#storage "Let's Encrypt - Traefik")

3. Add these labels to your docker compose service and Traefik will automatically add them.  
Don't forget to replace:  
    - *\<domain name\>* by the domain name of the service
    - *\<service\>* by your service name
    - *\<port\>* by the service's port you want Traefik to redirect to (optional)
```
...
<service>:
  ...
  labels:
    - traefik.http.routers.<service>.rule=Host("<domain name>")
    - traefik.http.services.<service>.loadbalancer.server.port=<port>
    - traefik.http.routers.<service>.tls=true
    - traefik.http.routers.<service>.tls.certresolver=letsencrypt
    - traefik.http.routers.<service>.entrypoints=web-secure
    - traefik.enable=true
...
```
> **Required labels:**
> 
> Redirect incoming requests to *\<domain name\>*. Details on Traefik rules [here](https://doc.traefik.io/traefik/routing/routers/#rule)
>
>     traefik.http.routers.<service>.rule=Host("<domain name>")
> 
> Enable tls on the service:
>
>     traefik.http.routers.<service>.tls=true
>
> Use certresolver *letsencrypt* configured in traefik.toml:
>
>     traefik.http.routers.<service>.tls.certresolver=letsencrypt
>
> Accept requests from *web-secure* entrypoint only:
>
>     traefik.http.routers.<service>.entrypoints=web-secure
>
> More info [here](https://doc.traefik.io/traefik/providers/docker/#exposedbydefault):
>
>     traefik.enable=true
> 
> **Optional labels:**
> 
> Redirect incoming traffic to port *\<port\>* of the service:
>
>     traefik.http.services.<service>.loadbalancer.server.port=<port>

4. Create an docker network with this command:  
    `docker network create web`

5. Add this at the end of your docker-compose file (you have to add it for every different docker-compose file)  
```
...
networks:
  web:
    external: true
```

6. And add the service to the network:  
```
...
<service>:
  ...
  networks:
    web:
...
```

> The network *web* enable traefik instance to communicate with other services from different docker-composes

7. Once everything is done, launch the traefik instance from *traefik-config* folder.  
    `docker-compose up`

# Congratulation, you can now access the Traefik dashboard on port 8080 of your server/PC!

### If you have any questions or suggestions, please open an issue or a pull request.
