# Section 6: Docker in the Real World

### Introduction
+ Labs galore
+ Dockerize a web app
+ become a docker cli pro
+ Persistence and volumes
+ Networking multiple containers together
+ Optimize and clean up
+ Run custom scripts

### A Simple Web Application With Docker

### Creating a Dockerfile (Part 1)
```
FROM python:3.7.5-alpine

RUN mkdir /app
WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY . .
```

### Creating a Dockerfile (Part 2)
```
LABEL maintainer="Edmund Magno <edmund.magno@nabepero.co.jp>" \
        version="1.0"

CMD flask run --host=0.0.0.0 --port=5000
```

### Building and Pushing Docker Images
```
docker image build -t web1 .
docker image inspect web1
docker image build -t web1:1.0 .
docker image inspect web1
docker image ls
docker image rm web1:1.0
docker login
docker image tag web1 edmundnabepero/web1:latest
docker image push edmundnabepero/web1:latest
docker image rm -f 9779
```

### Running Docker Containers
```
docker image tag edmundnabepero/web1 web1
docker image rm edmundnabepero/web1
docker image ls
docker container ls
docker container run -it -p 5000:5000 -e FLASK_APP=app.py web1
docker container ls -a
docker container rm hungry_bose
docker container run -it -p 5000:5000 -e FLASK_APP=app.py -d web1
docker container logs c0bd
docker container stats
docker container run -it --rm --name web1 -p 5000 -e FLASK_APP=app.py -d web1
docker container run -it --name web1_2 -p 5000 -e FLASK_APP=app.py -d --restart on-failure web1
docker container stop cranky_gates
docker container stop web1
docker container stop web1_2
```

### Live Code Reloading With Volumes
```
docker container run -it -p 5000:5000 -e FLASK_APP=app.py --rm --name web1 web1
docker container run -it -p 5000:5000 -e FLASK_APP=app.py --rm --name web1 -e FLASK_DEBUG=1 web1
docker image build -t web1 .
docker container run -it -p 5000:5000 -e FLASK_APP=app.py --rm --name web1 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/03-creating-a-dockerfile-part-1:/app" web1
```

### Debugging Tips and Tricks
+ change alpine to slim
```
docker container exec -it web1 sh
#change sh to bash if slim
docker container exec -it web1 flask --version
docker container exec -it web1 touch hi.txt

```

### Linking Containers With Docker Networks
```
docker image build -t web2 .
docker pull redis:3.2-alpine
docker network ls
ipconfig
docker network inspect bridge
docker container run --rm -itd -p 6379:6379 --name redis redis:3.2-alpine
docker container run -itd -p 5000:5000 -e FLASK_APP=app.py --name web2 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/09-linking-containers-with-docker-networks:/app" web2
docker exec redis ifconfig
docker exec web2 ifconfig
docker exec web2 ping 172.17.0.2
docker exec redis cat /etc/hosts
docker network create --driver bridge firstnetwork
docker network inspect firstnetwork
docker container stop web2
docker container stop redis
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork redis:3.2-alpine
docker container run -itd -p 5000:5000 -e FLASK_APP=app.py --name web2 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/09-linking-containers-with-docker-networks:/app" --rm --net firstnetwork web2
docker network inspect firstnetwork
docker exec web2 ping redis
docker exec -it redis redis-cli
KEYS *
INCRBY web2_counter 777
```

### Persisting Data to Your Docker Host
```
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork redis:3.2-alpine
docker container run -itd -p 5000:5000 -e FLASK_APP=app.py --name web2 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/09-linking-containers-with-docker-networks:/app" --rm --net firstnetwork web2
docker volume create web2_redis
docker volume ls
docker volume inspect web2_redis
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data redis:3.2-alpine
docker exec redis redis-cli SAVE
```

### Sharing Data Between Containers
```
docker image build -t web2 .
docker container run -itd -p 5000:5000 -e FLASK_APP=app.py --name web2 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/11-sharing-data-between-containers:/app" --rm --net firstnetwork web2
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data --volumes-from web2 redis:3.2-alpine
docker container exec -it redis sh
cd /app/public
ls -la
cat main.css
#stop containers then comment volume
docker image build -t web2 .
docker container run -itd -p 5000:5000 -e FLASK_APP=app.py --name web2 -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/11-sharing-data-between-containers:/app" --rm --net firstnetwork -v /app/public web2
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data --volumes-from web2 redis:3.2-alpine
docker container exec redis cat /app/public/main.css
```

### Optimizing Your Docker Images
+ docker image build -t weboptimized .
![image](https://user-images.githubusercontent.com/119827278/209298743-545d1a4f-817f-4270-8c7c-9d8e9ac7e399.png)

+ docker image ls
![image](https://user-images.githubusercontent.com/119827278/209298423-71e38d80-1fb9-45fb-8e0c-48eb8d0e45af.png)


### Running Scripts When a Container Starts
+ docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data redis:3.2-alpine
![image](https://user-images.githubusercontent.com/119827278/209300268-090fadd4-a247-4110-b471-ab479d2b03e8.png)
+ docker image build -t webentrypoint .
![image](https://user-images.githubusercontent.com/119827278/209300578-96601318-ccb9-42ed-994c-e89294f815e8.png)

+ docker container run -it -p 5000:5000 -e FLASK_APP=app.py --name webentrypoint -e FLASK_DEBUG=1 -v "/c/Users/Edmund G Magno Jr/diveintodocker/src/06-docker-in-the-real-world/13-running-scripts-when-a-container-starts:/app" --rm --net firstnetwork webentrypoint
![image](https://user-images.githubusercontent.com/119827278/209300991-8fdb4f54-e55e-4a79-b77b-2da2865506a7.png)

+ docker container run -it -p 5000:5000 -e FLASK_APP=app.py --name webentrypoint -e FLASK_DEBUG=1 -e WEB2_COUNTER_MSG="Docker fans have visited this page" --rm --net firstnetwork webentrypoint
![image](https://user-images.githubusercontent.com/119827278/209301585-5949e9cc-76b4-4139-b6b7-4e61e5195527.png)

### Cleaning Up After Yourself
![image](https://user-images.githubusercontent.com/119827278/209302963-27ee3a2e-9889-475b-88c5-a54ec3d351d3.png)
![image](https://user-images.githubusercontent.com/119827278/209303101-3e130f38-b67d-495a-990e-28faba7a5cd0.png)
![image](https://user-images.githubusercontent.com/119827278/209303240-9f4961f6-f6c7-4d79-9df6-8779df7136d4.png)
![image](https://user-images.githubusercontent.com/119827278/209304794-2dce770e-114f-4d72-a95f-3a7a21b58d7c.png)
