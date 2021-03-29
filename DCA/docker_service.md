# Create a simple service running the nginx image.
docker service create nginx

# Create an nginx service with a specified name, multiple replicas, and a published port.
docker service create --name nginx --replicas 3 -p 8080:80 nginx

# Use a template to pass the node hostname to each container as an environment variable.
docker service create --name node-hostname --replicas 3 --env NODE_HOSTNAME="{{.Node.Hostname}}" nginx

# Get the container running on the current machine, and print its environment variables to verify that the NODE_HOSTNAME variable is set properly.
docker ps
docker exec <CONTAINER_ID> printenv


# List the services in the cluster.
docker service 

# List the tasks for a service.
docker service ps nginx

# Inspect a service.
docker service inspect nginx
docker service inspect --pretty nginx

# Change a service.
docker service update --replicas 2 nginx

# Delete a service.
docker service rm nginx

# Create a global service. One task on each node
docker service create --name nginx --mode global nginx

# Two different ways to scale a service:
docker service update --replicas 3 nginx
docker service scale nginx=4
