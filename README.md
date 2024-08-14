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
    - `kubectl delete all -l app=name_of_app`: Delete all resources with a specific label.

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

# Service Discovery Issue: Missing K8S Environment Variables When Services Start in Incorrect Order

### Issue Explanation:

1. **Service Discovery with Kubernetes:**
    - Kubernetes provides a way for services to discover each other using DNS and environment variables. When a pod starts, Kubernetes sets up environment variables for each service that is currently available.
    - For example, if we have a service called `currency-exchange`, Kubernetes might set an environment variable like `CURRENCY_EXCHANGE_SERVICE_HOST` with the hostname or IP of the `currency-exchange` service.

2. **Problem Scenario:**
    - Suppose we have two services: `currency-conversion` and `currency-exchange`.
    - **If** the `currency-conversion` service pod starts **before** the `currency-exchange` service pod is up, the `currency-conversion` pod will not receive the environment variable `CURRENCY_EXCHANGE_SERVICE_HOST` because the `currency-exchange` service does not yet exist or is not yet available.
    - In this case, the `currency-conversion` pod will fail to discover the `currency-exchange` service because it doesn’t have the necessary environment variable set.

3. **Service Availability and Environment Variables:**
    - Once the `currency-exchange` service is up, Kubernetes will create environment variables for it. However, this happens after the initial creation of the pod. If the `currency-conversion` pod was started before `currency-exchange` was available, it won’t receive the updated environment variables dynamically.
    - Kubernetes doesn’t update environment variables of existing pods if a service becomes available later. Environment variables are set only once when the pod starts.

### Solution Explanation:

To avoid this problem, we can use a **custom environment variable** approach:

1. **Custom Environment Variable:**
    - Instead of relying on Kubernetes to automatically set environment variables for your services, explicitly define a custom environment variable in the pod’s deployment configuration.
    - For example, in the `currency-conversion` service's deployment configuration, we would set an environment variable named `CURRENCY_EXCHANGE_URI` with a value like `http://currency-exchange`.

2. **Advantages:**
    - By explicitly defining the custom environment variable, we ensure that your `currency-conversion` service has a consistent way to find the `currency-exchange` service, even if the `currency-exchange` service starts after the `currency-conversion` service pod.

3. **Implementation Steps:**
    - Modify the `deployment.yaml` for the `currency-conversion` service to include the custom environment variable.
    - Deploy the updated configuration, ensuring that the environment variable is available from the start.

### Summary:

- **Issue:** If `currency-conversion` starts before `currency-exchange`, it won’t get the `currency-exchange` environment variable and might fail.
- **Solution:** Define a custom environment variable in the deployment configuration to ensure consistent service discovery and avoid relying on dynamically-set environment variables that might not be available at startup.


# Centralized Configuration Management with Kubernetes ConfigMaps

In Kubernetes, to avoid hardcoding configuration values, you can use `ConfigMaps` for centralized configuration management.

1. **Create a ConfigMap**:
    - Use the command `kubectl create configmap confmap_name --from-literal=env_name=env_value` to create a ConfigMap with the key-value pair.
    - For example, `kubectl create configmap currency-conversion --from-literal=CURRENCY_EXCHANGE_URI=http://currency-exchange` creates a ConfigMap named `currency-conversion` with the key `CURRENCY_EXCHANGE_URI` and value `http://currency-exchange`.
    - As best practice, redirect the ConfigMap to a YAML file using `kubectl get configmap confmap_name -o yaml >> configmap.yaml` then move the ConfigMap to deployment configuration.    

2. **Verify ConfigMap**:
    - Check the ConfigMap using `kubectl get configmap` and `kubectl get configmap currency-conversion-service -o yaml` to view its YAML configuration.

3. **Update Deployment YAML**:
    - Add the ConfigMap configuration to your `deployment.yaml` file.
    - Comment out any hardcoded environment variables.
    - Reference the ConfigMap for environment variables in the deployment configuration.

4. **Apply Changes**:
    - Apply the updated `deployment.yaml` with `kubectl apply -f deployment.yaml`.
    - Ensure the new pod is launched and the service is operational.

5. **Benefits**:
    - ConfigMaps allow for centralized management of configuration data for microservices and different environments.

# Kubernetes Downtime and Zero Downtime Deployments
### Introduction to Kubernetes Downtime and Zero Downtime Deployments
- When deploying a new version of a service, mistakes such as incorrect image names can lead to failed deployments. Kubernetes, however, intelligently maintains the old version of the service to ensure continued operation. The challenge arises during the transition between service versions, where brief periods of downtime may occur if the new version takes time to start up while the old one is being shut down. This downtime is undesirable, and the issue is how to avoid it while ensuring smooth and reliable deployments.
`{"timestamp":"2024-08-14T16:03:43.772+00:00","status":500,"error":"Internal Server Error","path":"/currency-conversion-feign/from/USD/to/IN
  R/quantity/10"}`.
### Brief solution to downtime

The solution to avoid downtime during deployments in Kubernetes involves using ``Liveness`` and Readiness ``Probes``. These probes check the health and readiness of a microservice. The Readiness Probe ensures that traffic is only directed to a service when it's ready, preventing downtime during startup. The Liveness Probe monitors the ongoing health of the service and restarts it if necessary. By configuring these probes, Kubernetes can ensure smooth transitions between service versions, avoiding downtime by only terminating old versions once the new ones are fully operational.
spring actuator provides a health endpoint that can be used for readiness and liveness probes by just adding some properties in the application.properties file.