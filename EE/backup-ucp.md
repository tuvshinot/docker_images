# on the UCP server, get your UCP instance ID.
docker container run --rm \
  --name ucp \
  -v /var/run/docker.sock:/var/run/docker.sock \
  docker/ucp:3.1.5 \
  id

# Create an encrypted backup. Enter your UCP instance ID from the previous command for the --id flag.
docker container run \
  --log-driver none --rm \
  --interactive \
  --name ucp \
  -v /var/run/docker.sock:/var/run/docker.sock \
  docker/ucp:3.1.5 backup \
  --passphrase "secretsecret" \
  --id <Your UCP instance ID> > /home/cloud_user/ucp-backup.tar

# List the contents of the backup file.
gpg --decrypt /home/cloud_user/ucp-backup.tar | tar --list


# Restore UCP from Backup

# First, uninstall UCP on the UCP manager server.
docker container run --rm -it \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name ucp \
  docker/ucp:3.1.5 uninstall-ucp --interactive
# Restore UCP from the backup.
docker container run --rm -i --name ucp \
  -v /var/run/docker.sock:/var/run/docker.sock  \
  docker/ucp:3.1.5 restore --passphrase "secretsecret" < /home/cloud_user/ucp-backup.tar
