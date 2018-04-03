

### Run
```
vagrant up
```

### Connect
When the cluster is running you can connect to the manager node at 10.0.3.2 and start deploying services!

```
vagrant ssh manager
```

### commands
```
docker service create nginx
```

```
docker service update --publish-add 80 nginx  
```
```
docker service ls  
```
```
docker node ls  
```
```
docker service create --mode=global --name=web nginx  
```
```
docker service create --name my_web --replicas 3 --publish published=8080,target=80 nginx
```
```
docker node inspect — pretty worker-1 or docker node inspect self
```

### Notes
Note: because Ubuntu 16.04 ships with Python 3 instead of 2.x, we need to explicitly set the ansible_python_interpreter variable.

```
ansible.extra_vars = {
            ansible_python_interpreter: "/usr/bin/python3",
            manager_ip: manager_ip
        }
```

### Example

To start a service, you would need to have the following:

What is the Docker image that you want to run. we will run the standard nginx image that is officially available from the Docker hub.
We will expose our service on port 80.
We can specify the number of containers (or instances) to launch. This is specified via the replicas parameter.
We will decide on the name for our service.

What I am going to do then is to launch 5 replicas of the nginx container. To do that, I am again in the SSH into the manager node. And I give the following docker service create command:

```
docker service create --replicas 5 -p 80:80 --name web nginx
```
wait a bit ( few mins ) and run

```
docker service ps web
```
then a
```
docker service ls
```

you can then use the following command to scale up

```
docker service scale web=8
```

check the availability of a node using ```docker node inspect — pretty nodename```

```
Status:
 State: Ready
 Availability: Active
```
look for ```Availability: Active```

We can see that it is “Active” for its Availability attribute.

set the Availability to DRAIN. the Manager will stop tasks running on that node

```
docker node update --availability drain nodename
```

to remove the service
```
docker service rm web
```

### Issues  

run
```
sudo docker swarm join --token $(cat /vagrant/worker_token) 10.0.3.2:2377
```

from the worker node if it fails to connect

```
TASK [swarm join] **************************************************************
fatal: [worker-2]: FAILED! => {"changed": true, "cmd": "docker swarm join --token $(cat /vagrant/worker_token) :2377", "delta": "0:00:10.180178", "end": "2018-04-01 22:36:20.447872", "failed": true, "rc": 1, "start": "2018-04-01 22:36:10.267694", "stderr": "Error response from daemon: rpc error: code = Unavailable desc = grpc: the connection is unavailable", "stderr_lines": ["Error response from daemon: rpc error: code = Unavailable desc = grpc: the connection is unavailable"], "stdout": "", "stdout_lines": []}
```

running  docker node ls should state if worker-1 is part of the swarm
also provisioning the containers seems to work
```
vagrant up worker-2 --provision
```
