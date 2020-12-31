# Service Registration and Discovery in Spring Boot Microservices with Spring Cloud Consul

### Maven

* List all modules under maven project
```$ mvn help:evaluate -Dexpression=project.modules```

* Build and create docker images for all modules from parent
```$ mvn clean package spring-boot:build-image```

* Execute maven goal on specific module from parent
```
$ mvn clean spring-boot:run -pl service-a

$ mvn clean package spring-boot:build-image -pl service-a
```

### Gradle

* List all projects under gradle project
```$ gradle -q projects```

* Build and create docker images for all project from parent
```$ gradle clean build bootBuildImage```

* Execute gradle task on specific project from parent

```
$ gradle clean :service-a:bootRun

$ gradle clean :service-a:build :service-a:bootBuildImage
```

### Start HashiCorp Consul Container

* Run the below commands to start consul container which will be used to register spring boot application
```
$ docker run -d --name consul-server -p 8500:8500 -e CONSUL_BIND_INTERFACE=eth0 consul

-- Check logs for startup
$ docker logs consul-server

==> Found address '172.17.0.2' for interface 'eth0', setting bind option...
==> Starting Consul agent...
           Version: '1.9.1'
           Node ID: '597f4fef-fdcb-734a-37f1-14faefab6615'
         Node name: '962e56017069'
        Datacenter: 'dc1' (Segment: '<all>')
            Server: true (Bootstrap: false)
       Client Addr: [0.0.0.0] (HTTP: 8500, HTTPS: -1, gRPC: 8502, DNS: 8600)
      Cluster Addr: 172.17.0.2 (LAN: 8301, WAN: 8302)
           Encrypt: Gossip: false, TLS-Outgoing: false, TLS-Incoming: false, Auto-Encrypt-TLS: false
```

* Access Consul UI - http://127.0.0.1:8500

* Start Spring boot service

```
$ java -jar service-a\target\service-a-0.0.1-SNAPSHOT.jar

$ java -jar service-b\target\service-b-0.0.1-SNAPSHOT.jar

$ java -jar service-c\target\service-c-0.0.1-SNAPSHOT.jar
```

* Verify registered services in Consul UI

* Access Service A endpoint
```
$ curl http://localhost:8081/greeting
```

### Docker

* List Docker images
```$ docker images | grep spring-multi-module-consul```

* Run a docker image
```
$ docker run -d -p 8081:8080 -e spring.profiles.active=prod -e spring.cloud.consul.host=<HOST_IP> narramadan/spring-multi-module-consul-service-a

$ docker run -d -p 8082:8080 -e spring.profiles.active=prod narramadan/spring-multi-module-consul-service-b

$ docker run -d -p 8083:8080 -e spring.profiles.active=prod narramadan/spring-multi-module-consul-service-c
```

* List containers currently running
```$ docker ps```

* Stop running container
```$ docker stop <CONTAINER_ID>```

* List all containers that are running or stopped
```$ docker ps -a```

* Start a container which is not running
````$ docker start <CONTAINER_ID>```

* Delete docker container
```$ docker rm <CONTAINER_ID>```

* Delete docker image
```$ docker rmi <IMAGE_ID>```

* One liner to stop all running containers
```$ docker stop $(docker ps -a -q)```

* One liner to remove all stopped containers
```$ docker rm $(docker ps -a -q)```

### Docker Compose

* Validate docker-compose file and configuration
```$ docker-compose config```

* Builds, (re)creates, starts, and attaches to containers for a service.
```$ docker-compose up -d```

* Lists containers
```$ docker-compose ps```

* Stops running containers without removing them
```$ docker-compose stop```

* Starts existing containers for a service
```$ docker-compose start```

* Stops containers and removes containers, networks, volumes, and images created by `up`
```$ docker-compose down```

### Push to Docker Hub

* Login to Docker
```$ docker login```

* Tag docker image before pushing to docker hub if image name is not tagged with docker hub username
```
$ docker tab <IMAGE_ID> <Docker_Username>/<IMAGE_NAME>

$  docker tag ffc5ec760103 narramadan/springboot-servicea
```

* Push to docker hub
```
$ docker push <Docker_Username>/<IMAGE_NAME>

$ docker push narramadan/spring-multi-module-consul-service-a
```

### Deploy to Kubernetes

```$ kubectl cluster-info```

```$ kubectl explain deployment```

```$ kubectl explain deployment.spec.replicas```

```$ kubectl explain service```

* Apply deployments & services

- Order of creation is important. Services should be created before they are to be expanded

```$ kubectl apply -f k8s```

* List deployments, services & pods after applying the change
```$ kubectl get all```

* Watch pods by getting all pods or for a specific app
```
$ kubectl get pods --watch

$ kubectl get pods -l app=springboot-service-a --watch
```

* Scale deployment up/down by setting replicas
```$ kubectl scale --replicas=2 deployment/springboot-service-a```

* Get Environment Variables set to the pod
```$ kubectl exec <POD_NAME> -- printenv | grep SERVICE```

* Dump pod logs
```
$ kubectl logs <POD_NAME>

$ kubectl logs -f <POD_NAME>
```

* Delete pods and services
```$ kubectl delete -f k8s```

### References

* Verify if port is established to a process
```$ netstat -na | find "8081"```

* Only one LoadBalancer with Docker Desktop - https://stackoverflow.com/questions/59412733/kubernetes-docker-desktop-with-multiple-loadbalancer-services

* K8s desktop - https://collabnix.com/kubernetes-dashboard-on-docker-desktop-for-windows-2-0-0-3-in-2-minutes/

* Spring Cloud Consul - Common Application properties - https://cloud.spring.io/spring-cloud-consul/reference/html/appendix.html#common-application-properties

* Registering unique instanceId - https://programmer.ink/think/use-consul-to-implement-service-discovery-instance-id-customization-3-ways.html

* https://piotrminkowski.com/2019/11/06/microservices-with-spring-boot-spring-cloud-gateway-and-consul-cluster/

* https://spring.io/guides/gs/service-registration-and-discovery/

* https://docs.spring.io/spring-cloud-consul/docs/current/reference/html/index.html

* https://howtodoinjava.com/spring-cloud/consul-service-registration-discovery/

* https://springframework.guru/consul-miniseries-spring-boot-application-and-consul-integration-part-3/

* https://tanzu.vmware.com/content/slides/advanced-spring-boot-consul

* https://medium.com/@arv14/microservices-simplified-with-kubernetes-consul-ambassador-spring-python-go-terraform-a94b7da8dcc0