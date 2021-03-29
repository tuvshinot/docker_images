# Run a simple registry:
docker run -d -p 5000:5000 --restart=always --name registry registry:2
docker container stop registry && docker container rm -v registry

# Override the log level using an environment variable:
docker logs registry
docker container stop registry && docker container rm -v registry
docker run -d -p 5000:5000 --restart=always --name registry -e REGISTRY_LOG_LEVEL=debug registry:2
docker logs registry
docker container stop registry && docker container rm -v registry

# Secure the registry by generating an htpasswd file to be used for authentication:
mkdir ~/registry
cd ~/registry
mkdir auth
docker run --entrypoint htpasswd registry:2.7.0 -Bbn testuser password > auth/htpasswd

# Generate a self-signed certificate. When generating the cert, leave the prompts blank except for Common Name*. For *Common Name, put the public hostname of the registry server. The public hostname is in the playground interface:
mkdir certs
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt

# Run the registry with authentication and TLS enabled:
docker run -d -p 443:443 --restart=always --name registry \
  -v /home/cloud_user/registry/certs:/certs \
  -v /home/cloud_user/registry/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2

# Pull and search images on Docker hub:
docker pull ubuntu
docker search ubuntu

# Login to private registry, Provide certificate/turn off to Docker in order to login to private registry cuz docker do not trust this/ or
# Attempt to authenticate against the private registry:

docker login <registry public hostname>
Log in with the credentials we created earlier (testuser and password). A certificate signed by unknown authority message should pop up, because we are using a self-signed certificate.

# 1. way of login to private registry Configure docker to ignore certificate verification when accessing the private registry:

sudo vi /etc/docker/daemon.json
{
  "insecure-registries" : ["<registry public hostname>"]
}
# Restart docker:

sudo systemctl restart docker
Try docker login again:

docker login <registry public hostname>
This time it should work!

# 2. way of login to registry However, this method of accessing the registry is very insecure. It turns off certificate verification entirely, exposing us to man-in-the-middle attacks. So, let's do this the right way.
sudo mkdir -p /etc/docker/certs.d/<registry public hostname>

# Copy certs from registry host and put in the server you are trying to login from
sudo scp cloud_user@<registry public hostname>:/home/cloud_user/registry/certs/domain.crt /etc/docker/certs.d/<registry public hostname>
Try docker login:

docker login <registry public hostname>
: testuser
: password

Push to and pull from your private registry:
docker pull ubuntu
docker tag ubuntu <registry public hostname>/ubuntu
docker push <registry public hostname>/ubuntu
docker image rm <registry public hostname>/ubuntu
docker image rm ubuntu
docker pull <registry public hostname>/ubuntu
