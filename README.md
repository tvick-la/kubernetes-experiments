# Kubernetes Experiments
## Dependencies
To run kubernetes locally, I use [minikube](https://minikube.sigs.k8s.io/docs/).

[minikube requires a container or VM manager](https://minikube.sigs.k8s.io/docs/start/), such as: Docker, Hyperkit, etc.

I installed docker via [colima](https://github.com/abiosoft/colima):
```
brew install colima
```

Then I installed minikube:
```
brew install minikube
```

minikube installs `kubectl` as a dependency.

## Some commands
### `minikube`
Start minikube:
```
minikube start
```

Show status:
```
minikube status
```

Stop minikube
```
minikube stop
```

Open a minikube dashboard:
```
minikube dashboard
```

### `kubectl`
Show k8s resources:
```
kubectl get node
kubectl get service
kubectl get deployment
kubectl get replicaset
kubectl get pod
kubectl get all
```

#### Deployment CRUD
Create a new deployment:
```
kubectl create deployment mongo-depl --image=mongo
```

Edit a deployment (edits are automatically applied):
```
kubectl edit deployment <deployment-id>
```

Delete a deployment:
```
kubectl delete deployment mongo-depl
```

Apply deployment from a file:
```
kubectl apply -f 01-nginx-deployment.yaml
```

You can apply more than just deployments.  K8s automatically makes changes to meet the declared configuration.

Delete deployment specified in a file:
```
kubectl delete -f 01-nginx-deployment.yaml
```

Export the result of a deployment to yaml:
```
kubectl get deployment <deployment-id> -o yaml > some-file.yaml
```

#### Pod interactions
Describe a pod
```
kubectl describe pod <pod-id>
```

Show logs from a pod (can follow w/ `-f`)
```
kubectl logs [-f] <pod-id>
```

Enter a pod's shell:
```
kubectl exec -it <pod-id> -- bin/bash
```

# Creating a "full-stack" app
This app has:
- A Mongo DB running in a pod
- An internal service exposing MongoDB internally
- A Mongo Express UI running in a pod
- An external service exposing Mongo Express UI externally
- A secret to store Mongo DB creds

## Walk-through
1. Create secret containing mongo root usernamd & password
   ```
   kubectl apply -f 05-mongodb-secret.yaml
   ```

2. Create mongo DB deployment & internal service
   ```
   kubectl apply -f 06-mongodb-deployment.yaml
   kubectl apply -f 07-mongodb-service.yaml
   ```

3. Create config map containing internal service ports
   ```
   kubectl apply -f 08-mongo-config-map.yaml
   ```

4. Create mongo express deployment & external service
   ```
   kubectl apply -f 09-mongo-express-deployment.yaml
   kubectl apply -f 10-mongo-express-service.yaml
   ```

5. Expose external service from minikube (**`minikube service` does not work when using docker on a mac**)
   ```
   minikube tunnel
   ```

6. Navigate to [localhost:8081](http://localhost:8081)




# Resources
Mostly followed [this TechWorld with Nana tutorial on YouTube](https://www.youtube.com/watch?v=X48VuDVv0do)
