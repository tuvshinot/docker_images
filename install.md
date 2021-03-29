Install required packages:
sudo yum install -y device-mapper-persistent-data lvm2


Add the Docker CE repo:
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo


Install the Docker CE packages and containerd.io:
sudo yum install -y docker-ce-18.09.5 docker-ce-cli-18.09.5 containerd.io


Start and enable the Docker service:
sudo systemctl start docker
sudo systemctl enable docker


Add cloud_user to the docker group, giving the user permission to run docker commands:
sudo usermod -a -G docker cloud_user
Log out and back in.

Test the installation by running a simple container:
docker run hello-world
