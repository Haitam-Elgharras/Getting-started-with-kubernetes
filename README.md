# Kubernetes and Microservices

## Images

You can reuse these images instead of creating and pushing new container images

- Currency Exchange Service 
	- v11 - in28min/mmv3-currency-exchange-service:0.0.11-SNAPSHOT
  - v12 - in28min/mmv3-currency-exchange-service:0.0.12-SNAPSHOT
- Currency Conversion Service
	- in28min/mmv3-currency-conversion-service:0.0.11-SNAPSHOT
    - Uses CURRENCY_EXCHANGE_SERVICE_HOST
  - in28min/mmv3-currency-conversion-service:0.0.12-SNAPSHOT
    - Uses CURRENCY_EXCHANGE_URI

## URLS

#### Currency Exchange Service
- http://localhost:8000/currency-exchange/from/USD/to/INR

#### Currency Conversion Service
- http://localhost:8100/currency-conversion-feign/from/USD/to/INR/quantity/10


#### Commands

1. **Docker Commands:**
    - `docker run -p 8080:8080 in28min/hello-world-rest-api:0.0.1.RELEASE`: Run a Docker container from the `in28min/hello-world-rest-api` image and map port 8080 on your machine to port 8080 in the container.

2. **Kubernetes Deployment Commands:**
    - `kubectl create deployment hello-world-rest-api --image=in28min/hello-world-rest-api:0.0.1.RELEASE`: Create a deployment for the `hello-world-rest-api` app using the specified image.
    - `kubectl expose deployment hello-world-rest-api --type=LoadBalancer --port=8080`: Expose the deployment as a LoadBalancer service on port 8080.
    - `kubectl scale deployment hello-world-rest-api --replicas=3`: Scale the deployment to 3 replicas.
    - `kubectl delete pod hello-world-rest-api-58ff5dd898-62l9d`: Delete a specific pod.
    - `kubectl autoscale deployment hello-world-rest-api --max=10 --cpu-percent=70`: Autoscale the deployment to a maximum of 10 replicas based on CPU usage.
    - `kubectl edit deployment hello-world-rest-api #minReadySeconds: 15`: Edit the deployment and set the minimum number of seconds a pod should be ready.
    - `kubectl set image deployment hello-world-rest-api hello-world-rest-api=in28min/hello-world-rest-api:0.0.2.RELEASE`: Update the deployment to use a new image version.

3. **Google Cloud and Kubernetes Commands:**
    - `gcloud container clusters get-credentials in28minutes-cluster --zone us-central1-a --project solid-course-258105`: Configure kubectl to use the credentials for a specific Google Cloud Kubernetes cluster.
    - `kubectl get events --sort-by=.metadata.creationTimestamp`: List all events in the cluster, sorted by creation time.
    - `kubectl get componentstatuses`: Get the status of Kubernetes components.
    - `kubectl get pods --all-namespaces`: List all pods across all namespaces.

4. **Kubernetes Status and Debugging Commands:**
    - `kubectl get pods -o wide`: Get detailed information about all pods.
    - `kubectl explain pods`: Get detailed information about the pod resource.
    - `kubectl describe pod hello-world-rest-api-58ff5dd898-9trh2`: Get detailed information about a specific pod.
    - `kubectl get replicaset`: List all ReplicaSets.
    - `kubectl scale deployment hello-world-rest-api --replicas=3`: Scale the deployment to 3 replicas.
    - `kubectl set image deployment hello-world-rest-api hello-world-rest-api=DUMMY_IMAGE:TEST`: Update the deployment with a dummy image for testing.

5. **Kubernetes Service and Deployment Management:**
    - `kubectl create deployment currency-exchange --image=in28min/mmv3-currency-exchange-service:0.0.11-SNAPSHOT`: Create a deployment for the currency exchange service.
    - `kubectl expose deployment currency-exchange --type=LoadBalancer --port=8000`: Expose the currency exchange deployment as a LoadBalancer service on port 8000.
    - `kubectl get svc --watch`: Watch the status of services in real time.
    - `kubectl get deployments`: List all deployments.
    - `kubectl get deployment currency-exchange -o yaml >> deployment.yaml`: Export the deployment configuration to a YAML file.

6. **Kubernetes Rollout and Logs:**
    - `kubectl rollout history deployment currency-conversion`: View the rollout history of the currency conversion deployment.
    - `kubectl rollout undo deployment currency-exchange --to-revision=1`: Roll back the currency exchange deployment to a previous revision.
    - `kubectl logs currency-exchange-9fc6f979b-2gmn8`: Get logs from the specified pod.
    - `kubectl logs -f currency-exchange-9fc6f979b-2gmn8`: Follow the logs from the specified pod in real time.

7. **Kubernetes Horizontal Pod Autoscaling and ConfigMaps:**
    - `kubectl autoscale deployment currency-exchange --min=1 --max=3 --cpu-percent=5`: Autoscale the currency exchange deployment between 1 and 3 replicas based on CPU usage.
    - `kubectl create configmap currency-conversion --from-literal=CURRENCY_EXCHANGE_URI=http://currency-exchange`: Create a ConfigMap with a currency exchange URI.
    - `kubectl get configmap currency-conversion -o yaml >> configmap.yaml`: Export the ConfigMap to a YAML file.
    - `watch -n 0.1 curl http://34.66.241.150:8100/currency-conversion-feign/from/USD/to/INR/quantity/10`: Continuously watch the output of a currency conversion service.

8. **Docker Push Commands:**
    - `docker push in28min/mmv3-currency-exchange-service:0.0.11-SNAPSHOT`: Push the currency exchange Docker image to the registry.
    - `docker push in28min/mmv3-currency-conversion-service:0.0.11-SNAPSHOT`: Push the currency conversion Docker image to the registry.


## Useful Commands summary

1. **Create the docker image for your application (eg: spring boot)**
- `mvn spring-boot:build-image`

2. **Push the docker image to docker hub**
- `docker login`
- `docker push username/image-name:tag`

3. **Create a deployment in kubernetes**
- `kubectl create deployment deployment-name --image=username/image-name:tag`
- eg: `kubectl create deployment currency-exchange --image=haitamelgharras/mmv3-currency-exchange-service:0.0.11-SNAPSHOT`

4. **Expose the deployment as a service**
- `kubectl expose deployment deployment-name --type=LoadBalancer --port=port`
- eg: `kubectl expose deployment currency-exchange --type=LoadBalancer --port=8000`

5. **Test it using curl**
- `curl http://EXTERNAL-IP:PORT/endpoint`
- eg: `curl http://34.45.103.67:8000/currency-exchange/from/USD/to/INR`


### Service Discovery in Kubernetes
- Kubernetes auto-generates service environment variables when we create a service.
- `Automatic Service Discovery`: Service Environment Variables: Kubernetes injects environment variables into each Pod based on the Services available at the time the Pod is created. This allows Pods to easily discover and connect to Services.
- Pod name available via HOSTNAME variable.
- Service name available via SERVICE_NAME variable.
- We can call another service using ``SERVICE_NAME_SERVICE_HOST:PORT`` eg: ``CURRNECY_EXCHANGE_SERVICE_HOST:8000``


### Kubernetes Service and Deployment Management with yaml
1. **Notes**
- To get the yaml of a deployment or service and save it to a file
  - `kubectl get deployment currency-exchange -o yaml >> deployment.yaml`
  - `kubectl get svc currency-exchange -o yaml >> service.yaml`
- We can combine the deployment and service yaml into a single file by adding ``---`` between them
- If we make changes to the yaml file, we can apply the changes using `kubectl apply -f deployment.yaml`
- To see the diff of the changes that will be applied, we can use `kubectl diff -f deployment.yaml`

2. **Simplifying yaml**
- Simplify the deployment.yaml file by removing unnecessary fields:
  - Remove: Creation timestamp, generation, resource version, self link, UID, progress deadline, revision history limit, termination message path, termination message policy, DNS policy, schedule name, security context, termination grace period in seconds, and status.
  - Keep: Labels, name, namespace, replicas, selectors, strategy, and template.

3. **Deployment Configuration**

- Pod Template: Contains container specs such as image, image pull policy, and container name. Multiple containers can be specified within a pod.
- Restart Policy: Set to Always to ensure containers are restarted if they fail.
- Labels: Apply to both the pod and the deployment for identification. 
- Deployment Specs:
  - Replicas: Define the number of pod replicas.
  - Strategy: Use RollingUpdate to update pods incrementally.
  - Match Labels: Ensure deployment targets the correct pods.

4. **Service Configuration**
- Port: Define the port on which the service listens.
- Target Port: Define the port on which the service forwards requests to the pod.
- Service Specs:
  - Type: Set to LoadBalancer to expose the service externally.
  - Session Affinity: Set to None for REST APIs; session affinity is used for applications requiring all requests to go to the same instance.