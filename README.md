## Deployment options for KubeLambda components

There are few options for deploy or run KubeLambda components for testing of usage:

- Docker Compose. Simple and convenient way to run locally 
- (WIP) Kubernetes manifests. Simplest way to run everything in Kubernetes. 
- (TBD) Helm chart. Convenient way to deploy to Kubernetes.

## Notes on RocketMQ

I've included configs I tried to use with RocketMQ as _Broker_ together with docker-compose file I used. I abandoned it's usage, but this information may be useful to someone else. 
I encountered a few issues during creation of the RocketMQ with docker-compose such as:

- RocketMQ are run number of containers and none of them has any readiness/liveness checks implemented. If any other parts of the system depends on exaction order there is no reliable way to know are RocketMQ started and ready to accept clients. (it could be done with some script that checks logs from broker, but I considered it overkill)
- There is no good explanation of used ports in RocketMQ therefore I have to guess. 
- On k8s usage: since RocketMQ written on Java MEM values must be passed to the JM so there is no way to configure just Pod's resources without changing command itself, so no Vertical scaling for these pods. 
- Despite RocketMQ can create topics automatically, if there is no such topic, client will throw error. So I either need to wait for topic or create it in advance (which I did). This could be an issue for cases when I need to create topics dynamically for the new _Adapters_
