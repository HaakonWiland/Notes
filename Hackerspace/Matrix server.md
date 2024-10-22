TODO:
Fiks docker compose in infra folder, something wrong with the ports mapping/networks. 

fix volume definitions in the docker. 


Useful:
Start docker containers:
`docker compose up -d`

Check current containers:
`docker ps`

Start database container: 
`docker exec -it infra-synapse_db-1 /bin/sh`
-> get to password prompt:
`psql -U synapse -W`

quit database: ctrl + d


```
docker run -it --rm --mount type=volume,src=infra_synapse_data,dst=/data -e SYNAPSE_SERVER_NAME=matrix.hackerspace-ntnu.no  -e SYNAPSE_REPORT_STATS=yes matrixdotorg/synapse:latest generate
```

`hackerspace-dev@idi.ntnu.no`

Server name: matrix.hackerspace-ntnu.no 

Config file: `/var/lib/docker/volumes/infra_synapse_data/_data/homeserver.yaml`

output of docker ps 

- **`8008-8009/tcp`** means the container is listening on internal ports 8008 and 8009, but these ports are not exposed to your host machine.
- **`8008->8008/tcp`** means Docker is exposing port 8008 inside the container and mapping it to port 8008 on the host machine.





Functionalities: 
- Matrix server connected to new website where everyone gets an account based their website account. Accountname is the "feide name", but password is defined by user. 
- On the matrix there should not be possible to create a new account manually? -> instead gets imported from the website accounts?  


Q: How should the server be hosted; Should run on our own server, with docker.  
-> Q: Benefits of element hosting? 

Q: How to get website accounts imported into matrix? 


Own matrix folder on the server. It should have its own PostgreSQL database

Optimal: 
Have one docker compose file which contain both the database and synapse -> The guide currently have two different once 


