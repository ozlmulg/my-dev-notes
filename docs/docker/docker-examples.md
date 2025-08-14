# Docker Examples

___

## Container

**Running a container:**

`docker container run image:tag`

Example: `docker container run nginx:latest`
___
**Running a container in detached mode (-d):**

`docker container run -d image:tag`

Example: `docker container run -d nginx:latest`
___
**Starting a container with a different command instead of the default:**

`docker container run image:tag command`

Example: `docker container run ubuntu:latest ping 127.0.0.1`
___
**Running a container with a given name:**

`docker container run --name name image:tag`

Example: `docker container run --name container1 -d nginx:latest`
___
**Running another command inside a running container:**

`docker container exec container_id|or|container_name command`

Example: `docker container exec 12a793b3fec0 ping 127.0.0.1`
___
**Opening a shell inside a running container:**

`docker container exec -it container_id|or|container_name sh`

Example: `docker container exec -it 12a793b3fec0 sh`
___
**Creating a container with detached mode and shell connection (dit):**

`docker container run -dit image:tag sh`

Example: `docker container run -dit nginx:latest sh`
___
**Attaching to a container created with detached mode and shell:**

`docker attach container_id|or|container_name`

Example: `docker attach 12a793b3fec0`
___
**Stopping a container:**

`docker container stop container_id|or|container_name`

Example: `docker container stop 12a793b3fec0`
___
**Removing a container:**

`docker container rm container_id|or|container_name`

Example: `docker container rm 12a793b3fec0`
___
**Removing a running container (-f):**

`docker container rm -f container_id|or|container_name`

Example: `docker container rm -f 12a793b3fec0`
___
**Automatically remove container on exit (-rm):**

`docker container run -rm image:tag`

Example: `docker container run -rm nginx:latest`
(with -rm the container is automatically removed after stopping)
___
**Inspecting container details:**

`docker container inspect container_id|or|container_name`

Example: `docker container inspect 12a793b3fec0`
___
**Removing all containers (running and stopped) in the system:**

`docker container rm -f $(docker ps -aq)`
___
**Listing running containers:**

`docker container ls` or

`docker container ps`
___
**Listing all containers:**

`docker container ls -a` or

`docker container ps -a`
___
**Listing processes inside a running container:**

`docker top container_id|or|container_name`

Example: `docker top 12a793b3fec0`
___
**Viewing CPU, RAM, I/O usage of a running container:**

`docker stats container_id|or|container_name`

Example: `docker stats 12a793b3fec0`
___
**Limiting container memory usage (--memory, --memory-swap):**

`docker container run --memory=value(b,k,m,g) --memory-swap=value(b,k,m,g) image:tag`

Example: `docker container run --memory=1g --memory-swap=2g nginx:latest`
(With memory-swap you can also define swap space. b=byte, k=kilobyte, m=megabyte, g=gigabyte)
___
**Limiting container CPU usage (--cpus, --cpuset-cpus):**

`docker container run --cpus="number_of_cores" image:tag`

Example: `docker container run --cpus="3" nginx:latest`
(This limits the number of CPU cores the container can use)

`docker container run --cpuset-cpus="core_numbers" image:tag`

Example: `docker container run --cpuset-cpus="0,4" nginx:latest`
(This sets which CPU cores the container can access)
___
**Setting environment variables for a container:**

`docker container run --env environment_variable=value image:tag`

Example: `docker container run --env VAR1=test1 --env VAR2=test2 nginx:latest`
___
**Copying files between container and host (both directions):**

`docker cp container_id|or|container_name:path host_path`

Example: `docker cp 12a793b3fec0:/usr/src/app/ .`

___

## Image

**Logging into a registry via Docker CLI:**

`docker login registry_url`

Example: `docker login localhost:8080`
___
**Pulling an image to the system:**

`docker image pull image:tag`

Example: `docker image pull nginx:latest`
___
**Pushing an image to Docker Hub (or another repository):**

`docker image push repository/image:tag`

Example: `docker image push ozgurozturknet/adanzyedocker:latest`
___
**Tagging an existing image with a new tag:**

`docker image tag image:tag newimage:tag`

Example: `docker image tag nginx:latest ozgurozturknet/nginx:v1`
___
**Inspecting image details:**

`docker image inspect image:tag`

Example: `docker image inspect nginx:latest`
___
**Listing image layers:**

`docker image history image:tag`

Example: `docker image history nginx:latest`
___
**Building a new image using Dockerfile:**

`docker image build -t image:tag .`

Example: `docker image build -t ozgurozturknet/hello-world:latest .`
(Dockerfile must be in the folder where this command is run)
___
**Using build args when building image:**

`docker image build --build-arg arg=value -t image:tag .`

Example: `docker image build --build-arg VERSION=3.7.1 -t nginx:latest .`
___
**Listing all images in the system:**

`docker image ls`
___
**Removing an image from the system:**

`docker image rm image:tag`

Example: `docker image rm nginx:latest`
___
**Creating an image from a container:**

`docker commit container_id|or|container_name image:tag`

Example: `docker commit 12a793b3fec0 ozgurozturknet/img:latest`
___
**Saving an image to a file and loading an image from a saved file:**

`docker save image:tag -o filename.tar`

Example: `docker save ozgurozturknet/img:latest -o image.tar`

`docker load -i filename.tar`

Example: `docker load -i imagecon1.tar`

___

## Volume

**Creating a volume:**

`docker volume create volume_name`

Example: `docker volume create firstvolume`
___
**Inspecting volume details:**

`docker volume inspect volume_id|or|volume_name`

Example: `docker volume inspect firstvolume`
___
**Listing all volumes in the system:**

`docker volume ls`
___
**Mounting a volume to a container (-v):**

`docker container run -v volume_name:container_path image:tag`

Example: `docker container run -v firstvolume:/var/www/html image:tag`
___
**Mounting a volume as read-only (:ro):**

`docker container run -v volume_name:container_path:ro image:tag`

Example: `docker container run -v firstvolume:/var/www/html:ro image:tag`
___
**Binding a host folder or file as a mount:**

`docker container run -v host_folder_path:container_path image:tag`

Example: `docker container run -v c:\websites:/usr/share/nginx/html nginx:latest`
___
**Removing a volume:**

`docker volume rm volume_name`

Example: `docker volume rm firstvolume`

___

## Network

**Creating a user-defined bridge network (bridge):**

`docker network create --driver=bridge network_name`

Example: `docker network create --driver=bridge bridge-net`
___
**Creating a user-defined bridge network with IP settings:**

`docker network create --driver=bridge --subnet=cidr --ip-range=cidr --gateway=ip_address network_name`

Example:
`docker network create --driver=bridge --subnet=10.10.0.0/16 --ip-range=10.10.10.0/24 --gateway=10.10.10.10 bridge-net`
___
**Listing all networks in the system:**

`docker network ls`
___
**Inspecting network details:**

`docker network inspect network_name`

Example: `docker network inspect bridge-net`
___
**Running a container connected to a non-default network:**

`docker container run --network network_name image:tag`

Example: `docker container run --network bridge-net nginx:latest`
___
**Connecting a running container to another network:**

`docker network connect network_name container_id|or|container_name`

Example: `docker network connect bridge-net 12a793b3fec0`
___
**Disconnecting a running container from a network:**

`docker network disconnect network_name container_id|or|container_name`

Example: `docker network disconnect bridge-net 12a793b3fec0`
___
**Running a container with published ports (-p):**

`docker container run -p host_port:container_port/tcp_or_udp image:tag`

Example: `docker container run -p 8080:80 -p 53:53/udp nginx:latest`

___

## Logging

**Viewing logs created by a container:**

`docker logs container_id|or|container_name`

Example: `docker logs 12a793b3fec0`
___
**Viewing detailed logs in long format:**

`docker logs --details container_id|or|container_name`

Example: `docker logs --details 12a793b3fec0`
___
**Viewing logs within a specific date range:**

`docker logs --since date_time --until date_time container_id|or|container_name`

Example: `docker logs --since 2020-01-13T11:34:43.154304300Z 12a793b3fec0`
(since shows logs from the given time, until shows logs up to the given time)
___
**Viewing last N log entries:**

`docker logs --tail number container_id|or|container_name`

Example: `docker logs --tail 10 12a793b3fec0`
(lists the last 10 log entries)
___
**Following logs live:**

`docker logs -f container_id|or|container_name`

Example: `docker logs -f 12a793b3fec0`
(logs will show live as they occur; use Ctrl-C to exit)

___

## References

For more details, check
the [website](https://github.com/ozgurozturknet/AdanZyeDocker/blob/master/DockerCheatSheetTurkce.md).
