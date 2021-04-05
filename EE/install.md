# Provision a playground server with the Ubuntu 18.04 Bionic Beaver LTS image and a size of Large.
# You will need a Docker Hub account. You can create one at https://hub.docker.com.
# Start a Docker EE free trial: https://hub.docker.com/editions/enterprise/docker-ee-trial.
# Get your unique Docker EE URL from your trial:
# Go to https://hub.docker.com/my-content.
# Click Setup.
# Copy your url.

# On the playground server, set up some temporary environment variables. Enter your unique Docker EE URL for the DOCKER_EE_URL variable.
DOCKER_EE_URL=<your docker ee url>
DOCKER_EE_VERSION=18.09

# Install required packages.
sudo apt-get install -y \
  apt-transport-https \
  ca-certificates \
  curl \
  software-properties-common

# Add the GPG and apt repository using your Docker EE URL.
curl -fsSL "${DOCKER_EE_URL}/ubuntu/gpg" | sudo apt-key add -

sudo add-apt-repository \
  "deb [arch=$(dpkg --print-architecture)] $DOCKER_EE_URL/ubuntu \
  $(lsb_release -cs) \
  stable-$DOCKER_EE_VERSION"

# Install Docker EE.
sudo apt-get update
sudo apt-get install -y docker-ee=5:18.09.4~3-0~ubuntu-bionic

# Give cloud_user access to use Docker.
sudo usermod -a -G docker cloud_user

# Log out of the server and log back in again, then test your Docker EE installation.
docker version

