# Docker compose creation for prototype

I encontered a few issues during creation of the docker-compose such as:

- Rocketmq are run number of containers and none of them has any readiness/liveness checks impementeded. If any other parts of the system depends on execution order there is no relaible way to know are rocketmq started and ready to accept clients
- There is no good explenation of used ports in RocketMQ therefore I have to guess. 
- Since RocketMQ written on Java MEM values must be passed to the JM so there is no way to configure just Pod's resources without changing command itself, so no Vertical scaling for these pods. 
- Despite RocketMQ can create topics automatically, if there is no such topic, client will throw error. So I either need to wait for topic or create it on start (which I did)
