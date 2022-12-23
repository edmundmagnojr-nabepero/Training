# Section 5: Discovering Docker

### Introduction

### Hello World with Docker
```cmd
docker run hello-world
```

### Docker Images and Containers

Docker Image | Docker Container
--- | ---
Everythin you need to run your application | Act of running an image
No state and doesn't change | immutable
Download, build and run | 
class | instance
result of running a series of steps |

+ one image, many containers

```cmd
docker run -it alpine sh
ls -la
cd home
ls -la
touch myfile
ls -la
exit
```

### Downloading and Storing Docker
+ Docker hub is docker registry
+ Docker registry is a place where you can store docker images
+ Docker registry consists of a number of docker repository
+ A single docker repository lives a collection of docker images
+ Each docker images can be distinguish by a tag

```cmd
docker run docker.io/library/hello-world
```

+ create a docker hub account

### Explaining the Docker Build Process
+ docker commit ...
+ dockerfile - blueprint of docker image

**Docker Image**
+ has layers
+ download only what changed
+ build only what changed
