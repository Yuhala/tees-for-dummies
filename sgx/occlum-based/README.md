## Deploying SGX applications using Occlum LibOS
- Similar to Gramine, Occlum is a library OS which allows to run unmodified applications inside an SGX enclave. 

## Setup 
- To use Occlum, we can either download its repo from GitHub and build as described [here](https://occlum.readthedocs.io/en/latest/build_and_install.html), or use a Docker-based Occlum image with the Occlum runtime already setup. We shall use the latter approach. 
- First, you need to [install Docker](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).
- Configure Docker
```bash
sudo groupadd docker
sudo gpasswd -a $USER docker
```
- Create softlinks for SGX drivers used by Occlum containers
```bash
mkdir -p /dev/sgx
ln -sf ../sgx_enclave /dev/sgx/enclave
ln -sf ../sgx_provision /dev/sgx/provision
```

## Build and run a program with Occlum 
- Follow the instructions in this [Startup guide](https://occlum.readthedocs.io/en/latest/quickstart.html#) to run a simple program secured in SGX using Occlum. We do something similar below.

- Create a Dockerfile as shown [here](./Dockerfile)
- Build and run the Docker container as follows
```bash
docker build -t occlum-hello . # builds the container from Dockerfile in the same director
docker run -it --device /dev/sgx/enclave --device /dev/sgx/provision occlum-hello # launch the container
```
- Once you have access to the container's terminal, you can build and run an SGX-protected application as follows.
```bash
make occlum-hello
mkdir occlum_instance && cd occlum_instance
occlum init
cp ../occlum-hello image/bin/
occlum build
occlum run /bin/occlum-hello 
```
- You can adapt the simple helloworld program to compile and run something more complex. 

- Stop and remove all docker containers
```bash
docker stop $(docker ps -a -q) # stop all containers
docker rm $(docker ps -a -q) # remove 
```