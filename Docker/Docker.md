- images are just operating systems and we can install different software
- containers are like different machines
- images can run on differetn containers like same os run on different systems
- by default we use bridge network so we have to map port but if we use host network then in means container and our system are using same network so there is no need of port mapping.
- container runs on same network can communicate to each other thats why we can create our custom network.

## network

- **bridge** : uses different network, port mapping needed, default selected.
- **host** : uses same network as device, port mapping does not needed
- **none** : no connection, can't access internet.

# Commands

- **docker start <container_name>** : run container

- **docker stop <container_name>** : stop container

- **docker exec -it <container_name> bash** : this is use to execute a container .(here it stands for _interactive_ thats means it connects my terminal to docker terminal.)

- **docker images** : to show all images.

- **docker run -it <image_name>** : run new container

- **docker run -it \_\_\_\_\_\_\_ <image_name>**

  - **--name<container_name>** : to give custom name to a new container
  - **-p <my_port>:<container_port>** : this expose container port and maps it to the given port.
  - **-e key:value** : this expose container environment variable
  - **--network=<network_name>** : by default it is bridge. You can also give custom name.

- **docker compose \_\_\_\_\_\_\_\_**

  - **up** : run all containers which you configure at once.
  - **up -d** : run all containers which you conf
  - **down** : delete all containers which you configure at once.

- **docker container ls** : used to list all active containers.

- **docker container ls -a** : used to list all containers.
  igure at once in background.

- **docker network ls** : to show all networks

- **docker network inspect <network_name>** : to show all details about that network.

- **docker network create -d <network_type> <custom_name>** : it create your custom network.

- **docker build . -t <image_name>** : it builds you image according to dockerfile.
- **docker pull <image_name>** : it pulls image from docker hub.
- **docker push <image_name>** : it pushes image to docker hub.
