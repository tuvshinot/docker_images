# Install required packages:
sudo yum install -y device-mapper-persistent-data lvm2


# Add the Docker CE repo:
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo


# Install the Docker CE packages and containerd.io:
sudo yum install -y docker-ce-18.09.5 docker-ce-cli-18.09.5 containerd.io


# Start and enable the Docker service:
sudo systemctl start docker
sudo systemctl enable docker


# Add cloud_user to the docker group, giving the user permission to run docker commands:
sudo usermod -a -G docker cloud_user
Log out and back in.

# sysTest the installation by running a simple container:
docker run hello-world


#####################################################################################

# Install Docker Compose.
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version

# Set up a Docker Compose project.
mkdir nginx-compose
cd nginx-compose
vi docker-compose.yml

# Create your docker-compose.yml.

version: '3'
services:
  web:
    image: nginx
    ports:
    - "8080:80"
  redis:
    image: redis:alpine

# Start your Compose app.
docker-compose up -d

# List the Docker Compose containers, then stop the app.
docker-compose ps
docker-compose down
