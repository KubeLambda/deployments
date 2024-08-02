## Deployment options for KubeLambda components

There are few options for deploy or run KubeLambda components for testing of usage:

- Docker Compose. Simple and convenient way to run locally 
- (WIP) Kubernetes manifests. Simplest way to run everything in Kubernetes. 
- (TBD) Helm chart. Convenient way to deploy to Kubernetes.

## Local run for testing with docker-compose

Notice I've commented example lambda in compose file. This is because I used example committed within Function repository instead the one in example-lambda-* repos.
I've end up usually doing it this way (using [python _Function_](https://github.com/KubeLambda/example-lambda-py) as example):

- Run docker-compose with `docker-compose up --build --remove-orphans` to spin-up _Adapter_ and _Broker_
- Run following command to build and run lambda function from [example_lambda folder](https://github.com/KubeLambda/kl-function-py/tree/main/example/example_lambda) in separate terminal:

```sh
docker build --no-cache -t function-python . ; docker run --network host --rm -it $(docker build -q example/example_lambda/)
```

and test requests (for [WebHookAdapter](https://github.com/KubeLambda/kl-webhook-adapter)) using following curl:

```sh
curl --data '{"name":"bob"}' --header 'Content-Type: application/json' http://0.0.0.0:3001/api/adapte
```

## Notes on RocketMQ

I've included configs I tried to use with RocketMQ as _Broker_ together with docker-compose file I used. I abandoned it's usage, but this information may be useful to someone else. 
I encountered a few issues during creation of the RocketMQ with docker-compose such as:

- RocketMQ are run number of containers and none of them has any readiness/liveness checks implemented. If any other parts of the system depends on exaction order there is no reliable way to know are RocketMQ started and ready to accept clients. (it could be done with some script that checks logs from broker, but I considered it overkill)
- There is no good explanation of used ports in RocketMQ therefore I have to guess. 
- On k8s usage: since RocketMQ written on Java MEM values must be passed to the JM so there is no way to configure just Pod's resources without changing command itself, so no Vertical scaling for these pods. 
- Despite RocketMQ can create topics automatically, if there is no such topic, client will throw error. So I either need to wait for topic or create it in advance (which I did). This could be an issue for cases when I need to create topics dynamically for the new _Adapters_
