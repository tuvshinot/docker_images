Back up DTR

# On the DTR server, get the DTR replica ID.
docker volume ls

#Look for a volume name that begins with dtr-registry-. The string of letters and numbers at the end of this volume name us your DTR replica ID.

#Back up the registry images.
sudo tar -zvcf dtr-backup-images.tar \
  $(dirname $(docker volume inspect --format '{{.Mountpoint}}' dtr-registry-<replica-id>))

#Back up DTR metadata.
read -sp 'ucp password: ' UCP_PASSWORD; \
docker run --log-driver none -i --rm \
  --env UCP_PASSWORD=$UCP_PASSWORD \
  docker/dtr:2.6.6 backup \
  --ucp-url https://<UCP Manager Private IP> \
  --ucp-insecure-tls \
  --ucp-username admin \
  --existing-replica-id <replica-id> > dtr-backup-metadata.tar


#Restore DTR Backup

#Stop the existing DTR replica.
docker run -it --rm \
  docker/dtr:2.6.6 destroy \
  --ucp-insecure-tls \
  --ucp-username admin \
  --ucp-url https://<UCP Manager Private IP>

#Restore images.
sudo tar -xzf dtr-backup-images.tar -C /var/lib/docker/volumes

#Restore DTR metadata.
read -sp 'ucp password: ' UCP_PASSWORD; \
docker run -i --rm \
  --env UCP_PASSWORD=$UCP_PASSWORD \
  docker/dtr:2.6.6 restore \
  --dtr-use-default-storage \
  --ucp-url https://<UCP Manager Private IP> \
  --ucp-insecure-tls \
  --ucp-username admin \
  --ucp-node <hostname> \
  --replica-id <replica-id> \
  --dtr-external-url <dtr-external-url> < dtr-backup-metadata.tar
