
Note: because Ubuntu 16.04 ships with Python 3 instead of 2.x, we need to explicitly set the ansible_python_interpreter variable.

```
ansible.extra_vars = {
            ansible_python_interpreter: "/usr/bin/python3",
            manager_ip: manager_ip
        }
```

Run
```
vagrant up
```

Connect
```
When the cluster is running you can connect to the manager node at 10.0.3.2 and start deploying services!
```

commands
```
docker service create nginx 
docker service update --publish-add 80 nginx  
docker service ls  
docker node ls  
docker service create --mode=global --name=web nginx  

docker service create --name my_web \
                        --replicas 3 \
                        --publish published=8080,target=80 \
                        nginx
```

issues  

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
