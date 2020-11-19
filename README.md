# Traefik v2.3.2 configuration
This is the docker compose and configuration for an instance of Traefik v2.3.2

> You can find a quick draft for v1.7 configuration in [an older commit](https://github.com/lbarbaglia/traefik-config/tree/4f17260d657d069c485c4bffea6b3e23753f0a32)

## Features
- HTTP redirect to HTTPS
- Let's Encrypt auto generated certificates

## Installation
You first have to create an empty "acme.json" file at the base of this folder.  
Then set the correct permissions.  
`touch acme.json & chmod 600 acme.json`  

Add these labels to your docker compose service and Traefik will automatically add them  
Don't forget to replace:
- <domain name> by the domain name of the service
- <port> by the service's port you want Traefik to redirect to

```
...
my-service:
  ...
  labels:
    - traefik.http.routers.account.rule=Host("<domain name>")
    - traefik.http.services.account.loadbalancer.server.port=<port>
    - traefik.http.routers.account.tls=true
    - traefik.http.routers.account.tls.certresolver=letsencrypt
    - traefik.http.routers.account.entrypoints=web-secure
    - traefik.enable=true
...
```

Create an docker network with this command:  
`docker network create web`

Add this at the end of your docker-compose file (you have to add it for every different docker-compose file)  
```
...
networks:
  web:
    external: true
```

And add the service to the network:  
```
...
my-service:
  ...
  networks:
    web:
...
```

### If you have any questions or suggestions, please open an issue or a pull request.
