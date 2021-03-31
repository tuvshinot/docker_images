# Install Docker Engine on the Swarm Manager: Ubuntu (CentOS like in install.md)

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


# Now initialize the swarm!
Note: Be sure to use the private IP (NOT the public IP) for the --advertise-addr:
docker swarm init --advertise-addr <swarm manager private IP>

# This shows some basic information about the current status of the swarm:
docker info

# List the current nodes in the swarm and their status:
docker node ls