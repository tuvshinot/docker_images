# Install Docker Engine on both worker nodes: Ubuntu

sudo apt-get update

sudo apt-get -y install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce=5:18.09.5~3-0~ubuntu-bionic docker-ce-cli=5:18.09.5~3-0~ubuntu-bionic containerd.io

sudo usermod -a -G docker cloud_user


# Get a join token from the manager. Run this command on the swarm manager:
docker swarm join-token worker

# Now copy the docker swarm join command provided in the output and run it on both workers:
docker swarm join --token <token> <swarm manager private IP>:2377

# On the swarm manager, verify that the two worker nodes have successfully joined:
docker node ls
