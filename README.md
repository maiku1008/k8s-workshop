# Kubernetes workshop

[Slides](https://hackmd.io/@99XCjSrYT7Cjoc69wdmSeQ/r1uGxphyh)

## Requirements
- Docker desktop with Kubernetes plugin
- `kubectl`

## Optional: kubernetes dashboard
```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
Follow instructions on https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

```
$ k proxy
```

login on http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/deployment?namespace=default

### Running a workload in a pod

```
$ cd webapp-color
$ docker build -t webapp-color:v1 .
$ cd ../pod
$ k apply -f .
```

Inspect the cluster with:
```
$ k get pods
NAME     READY   STATUS    RESTARTS   AGE
webapp   1/1     Running   0          5s
$ k get service
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
webapp-service   LoadBalancer   10.105.28.172   localhost     8080:31872/TCP   52s
$ k logs webapp
$ k describe pod webapp
```

Navigate to http://localhost:8080 to see the Flask application

Delete the resources with
```
$ k delete pod/webapp svc/webapp --grace-period=0 --force
```

### Running a stateless production workload (deployment)

```
$ cd ../deployment
$ less deployment.yaml
$ k apply -f .
```

Add replicas in deployment.yaml
```
$ k apply -f deployment.yaml
$ k get replicaset
```

```
$ cd ../webapp-color
$ docker build -t webapp-color:v2 .
$ cd ../deployment
```

Change image in deployment.yaml
```
$ k apply -f deployment.yaml
$ k get replicaset
# watch changes
```
```
$ k set image deploy/webapp webapp=wrong_image
$ k rollout status deploy/webapp
$ k rollout history deploy/webapp
$ k rollout undo deploy/webapp
```

```
$ k delete -f .
```

### Running a stateful production workload (statefulset)

```
$ cd ../statefulset
$ k apply -f .
```

### Configuring a workload with a configmap
```
$ cd ../configmap
$ k apply -f .
$ k edit cm webapp-config
$ k rollout restart deployments/webapp
```

### Configuring a workload with a secret

```
$ cd ../secret
$ k apply -f .
```
