## Setup

To start a P100 VM - Pls change location, host name, domain, ssh key

```
ibmcloud sl vs create --datacenter=lon06 --hostname=p100 --domain=ucb.com --image=2263543 --billing=hourly  --network 1000 --key=<KeyID> --flavor AC1_8X60X100 --san
```

SSH into the P100 VM, then setup s3fs to mount IBM cloud storage.

```
sudo apt-get update
sudo apt-get install automake autotools-dev g++ git libcurl4-openssl-dev libfuse-dev libssl-dev libxml2-dev make pkg-config
git clone https://github.com/s3fs-fuse/s3fs-fuse.git

cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install

```
Go back to root directory

```
echo "bf87c595976145c386349f53e2517493:a61ba4b36c06b17ce4a5cf1cb087821b79fb293c42b1e617" > $HOME/.cos_creds
chmod 600 .cos_creds
```

Clone this repo and mount to my IBM Object storage

```
git clone https://github.com/abhisha1991/w251-project.git
cd w251-project
mkdir data

sudo s3fs audiodata /root/w251-project/data -o passwd_file=$HOME/.cos_creds -o sigv2 -o use_path_request_style -o url=https://s3.jp-tok.cloud-object-storage.appdomain.cloud
```

build docker container

```
# copy the ipynb files to the docker folder for building the container
cp *.ipynb ~/w251-project/docker
cd docker
docker build -t tf/w251-project -f Dockerfile.tf-w251-project .
```

docker run

```
nvidia-docker run -d --name w251-project -p 8888:8888 -v /root/w251-project:/project tf/w251-project
docker logs w251-project
```

Then you can access the notebook as usual.


