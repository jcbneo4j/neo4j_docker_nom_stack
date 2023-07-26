# Neo4j Docker NOM Stack
This read me includes the steps to quickly deploy a Neo4j Ops Manager (NOM) stack in a Docker environment to evaluate NOM. This is an (unoffical) consolidation of steps (with updates) to compliment the [NOM Docker First Look Guide](https://neo4j.com/docs/ops-manager/current/first-look/docker-first-look/). 

# Prerequisites

The project runs on docker compose so having docker/docker compose is necessary (tested with Docker v20.10.14/Docker Compose V2).

# Steps to follow

* Update hosts files as per [Prepare hosts file](https://neo4j.com/docs/ops-manager/current/first-look/docker-first-look/#_prepare_hosts_file)
* Create as per [Create an empty directory for self-signed certificates](https://neo4j.com/docs/ops-manager/current/first-look/docker-first-look/#_create_an_empty_directory_for_self_signed_certificates)
* Clone the project: 
* Navigate to the root of the project
* Run: docker-compose up
* [Login to the NOM UI](https://server:8080/) (default username/password is admin/passw0rd)
* [Manually register agent] https://neo4j.com/docs/ops-manager/current/addition/agent-installation/manual/#register Note: save the config
* In a separate terminal window, run as follows to identify the container for the agent config (note sample output below):
*     docker ps    
```
CONTAINER ID   IMAGE                            COMMAND                  CREATED          STATUS                    PORTS                                                           NAMES
f4f518362635   neo4j/neo4j-ops-manager-server   "sh -c 'java -jar ap…"   33 minutes ago   Up 33 minutes             0.0.0.0:8080->8080/tcp, 0.0.0.0:9090->9090/tcp                  nom-server-1
210a2deee414   neo4j:5.5.0-enterprise           "tini -g -- /startup…"   33 minutes ago   Up 33 minutes (healthy)   7473-7474/tcp, 7687/tcp, 0.0.0.0:10000-10001->10000-10001/tcp   nom-db-single-1
e788183c620c   neo4j:4.4.8-enterprise           "tini -g -- /startup…"   33 minutes ago   Up 33 minutes (healthy)   7473-7474/tcp, 7687/tcp, 0.0.0.0:9000-9001->9000-9001/tcp       nom-storage-1
```
* From the sample output above, the CONTAINER ID for the nom-db-single-1 is 210a2deee414 - this will be a different ID for your instance of the container.
* Run the following to exec into the continer (using your container ID): docker exec -it 210a2deee414 /bin/bash
* Once inside the shell the prompt should look something like this -> root@db-single:/var/lib/neo4j#
* From the prompt, run the following two commands to update the container's environment to the token config from the agent registration step above, using the values obtained in the UI for CONFIG_TOKEN_CLIENT_ID and CONFIG_TOKEN_CLIENT_SECRET:
```
export CONFIG_TOKEN_CLIENT_ID=9467b11b-d2b8-4ce4-800e-6188f791153d

export CONFIG_TOKEN_CLIENT_SECRET=TXy28bBZkppRwJoaVU06pSJauhfRDembAqUTYK7naGwCqT8nu901QShva3pHz313
```

* Download and unpackage the latest NOM agent binary (1.7 at this time) with the following two commands:

wget https://dist.neo4j.org/ops-manager/1.7.1/neo4j-ops-manager-agent-1.7.1-linux-amd64.tar.gz?_ga=2.255553032.1609962748.1690211691-315406528.1687617214

tar xvf neo4j-ops-manager-agent-1.7.1-linux-amd64.tar.gz?_ga=2.255553032.1609962748.1690211691-315406528.1687617214

* Start the agent with the following command:

neo4j-ops-manager-agent-1.7.1/bin/agent console

Once started (after a few seconds), return to the NOM UI and the NOM home page should be populated with the Neo4j instance in your Docker stack. 




## Data Model
