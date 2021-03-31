# For this lesson, use a CentOS 7 server with a size of Small. Before starting the lesson, you'll first need to install Docker.
# Add a new storage device to your server. In Playground, select Actions, then select Add /dev/nvme1n1 and wait for it to finish adding the device.
# Stop and disable Docker.
sudo systemctl disable docker
sudo systemctl stop docker

# Delete any existing Docker data.
sudo rm -rf /var/lib/docker

# Configure DeviceMapper in daemon.json.
sudo vi /etc/docker/daemon.json
{
  "storage-driver": "devicemapper",
  "storage-opts": [
    "dm.directlvm_device=/dev/nvme1n1",
    "dm.thinp_percent=95",
    "dm.thinp_metapercent=1",
    "dm.thinp_autoextend_threshold=80",
    "dm.thinp_autoextend_percent=20",
    "dm.directlvm_device_force=true"
  ]
}

# Start and enable Docker.
sudo systemctl enable docker
sudo systemctl start docker

# Check the storage driver information provided by docker info.
docker info

# Run a container to verify that everything is working.
docker run hello-world
