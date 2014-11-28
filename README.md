
Mesos Docker images
====================


- mesos : mesos base image. No entrypoit.
- mesos-master : mesos master server. 
- mesos-slave : mesos slave server. 
- chronos : chronos server. 

Each images contain zookeeper. 

using example
-------

The below ``fig.yml`` is the example for 1 master / 2 slave / 1 chronos. 

This mesos cluster use zookeeper server in ``mesos0``. 
And choronos uses the zookeeper in itself for ``chronos`` status.

For more detail, see ``README.md`` in each images.

```
mesos0:
   image: oddpoet/mesos-master
   hostname: mesos-master
   command:
      - "--port=5050"
   ports: 
      - "5050:5050"
   expose:
      - "2181:2181"
mesos1:
   image: oddpoet/mesos-slave
   hostname: mesos-slave1
   command:
      - "--zk=zk://zk-server:2181/mesos"
      - "--port=5051"
   ports: 
      - "5051:5051"
   links:
      - mesos0:zk-server
mesos2:
   image: oddpoet/mesos-slave
   hostname: mesos-slave2
   command:
      - "--zk=zk://zk-server:2181/mesos"
      - "--port=5052"
   ports: 
      - "5052:5052"
   links:
      - mesos0:zk-server
chronos:
  image: oddpoet/chronos
  hostname: chronos 
  command:
    - "--master=zk://zk-server:2181/mesos"
    - "--zk-hosts=localhost:2181"
    - "--port=8081"
  ports:
    - "8081:8081"
  links:
    - mesos0:zk-server
```
