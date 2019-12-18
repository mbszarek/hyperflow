# HyperFlow: a scientific workflow management system

## Description

HyperFlow is a Workflow Management System (WMS) dedicated for scientific workflows. 

Browse the [wiki pages](https://github.com/balis/hyperflow/wiki) to learn more about the HyperFlow workflow model. 

## Getting started

The latest release of HyperFlow is 1.3.0

### Installation
* Install Node.js (http://nodejs.org)
* Install Redis (http://redis.io) 
* Install HyperFlow: <br>`npm install https://github.com/hyperflow-wms/hyperflow/archive/v1.3.0.tar.gz`
* For latest features, install from the master branch: Install HyperFlow:<br>`npm install https://github.com/hyperflow-wms/hyperflow/archive/master.tar.gz
* Add `<install_root>/node_modules/.bin` to your path

### Running locally
* Start the redis server: `redis-server`
* Run example workflows using command `hflow run <wf_directory>`, for example:<br>```hflow run ./examples/Sqrsum```

### Running locally using Docker images
* Use the latest Docker image for the HyperFlow engine, published in Docker Hub as `hyperflowwms/hyperflow` 
* You can build the image yourself: `make container`
* Start redis container:<br> 
```docker run -d --name redis redis --bind 127.0.0.1```
* Run workflow via HyperFlow container, for example:
```
docker run -a stdout -a stderr --rm --network container:redis \
       -e HF_VAR_WORKER_CONTAINER="hyperflowwms/soykb-worker" \ 
       -e HF_VAR_WORK_DIR="$PWD/input" \ 
       -e HF_VAR_HFLOW_IN_CONTAINER="true" \
       -e HF_VAR_function="redisCommand" \
       -e REDIS_URL="redis://127.0.0.1:6379" \
       --name hyperflow \
       -v /var/run/docker.sock:/var/run/docker.sock \
       -v $PWD:/wfdir \
       --entrypoint "/bin/sh" hyperflowwms/hyperflow -c "apk add docker && ls /wfdir && hflow run /wfdir"
```
Where
* `hyperflowwms/soykb-worker` is the name of the workflow worker container ([Soykb](https://github.com/hyperflow-wms/soykb-workflow) in this case)
* current directory contains `workflow.json`
* subdirectory `inputs` contains workflow input data 

Outputs:
* Directory `inputs` will contain files generated by the workflow run
* Directory `inputs/logs-hf` will contain logs of all workflow jobs

### Running in a distributed infrastructure using the RabbitMQ executor
* Start the RabbitMQ container: `docker run -d --name rabbitmq rabbitmq:3`
* Add option `-e AMQP_URL=amqp://rabbitmq`
* ...
