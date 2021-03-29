# Create the Backup
# On the manager:
sudo systemctl stop docker
sudo tar -zvcf backup.tar.gz -C /var/lib/docker/swarm .
sudo systemctl start docker

# Restore from Backup
# On the manager:
sudo systemctl stop docker
sudo rm -rf /var/lib/docker/swarm/*
sudo tar -zxvf backup.tar.gz -C /var/lib/docker/swarm/
sudo systemctl start docker
docker node ls