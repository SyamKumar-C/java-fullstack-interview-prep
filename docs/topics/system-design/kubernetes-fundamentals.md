# Kubernetes Fundamentals for Application Developers

> Priority: P2 | Level: Intermediate | Study time: 45–55 minutes

## 1. Short interview answer
Kubernetes orchestrates containers across a cluster. A Deployment manages replicated Pods and rolling updates; a Service provides stable networking; ConfigMaps and Secrets provide configuration; probes help manage health; resource requests and limits guide scheduling and resource control.

## 2. Core objects
- Cluster: control plane and worker nodes.
- Node: machine that runs workloads.
- Pod: smallest deployable unit; contains one or more containers.
- Deployment: desired replica count and rollout strategy for stateless workloads.
- ReplicaSet: maintains a desired number of Pods.
- Service: stable network endpoint for a set of Pods.
- Ingress/Gateway: HTTP routing into the cluster, depending on installed controller.
- ConfigMap: non-secret configuration.
- Secret: sensitive configuration; still requires encryption/access controls appropriate to the cluster.

## 3. Deployment example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-api
  template:
    metadata:
      labels:
        app: order-api
    spec:
      containers:
        - name: app
          image: example/order-api:1.4.2
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: "250m"
              memory: "512Mi"
            limits:
              cpu: "1"
              memory: "1Gi"
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
```

A readiness failure removes the Pod from service endpoints; a liveness failure can cause a restart. Startup probes help slow-starting applications avoid premature liveness failures.

## 4. Requests and limits
Requests influence scheduling. Limits cap resource use. CPU limits can throttle a container; memory limit excess can cause an OOM kill. Set values using observed load and JVM/container behavior, not guesses alone.

## 5. Service discovery and scaling
Pods are replaceable and their IPs can change. Clients should use Service DNS, not Pod IPs. Horizontal Pod Autoscaler can scale replicas based on supported metrics. Scaling replicas does not fix a saturated database or an application with unsafe shared state.

## 6. Configuration and secrets
Keep environment configuration outside container images. Restrict Secret access with RBAC, use encryption at rest where configured, and prefer an external secret manager integration for sensitive production secrets. Do not print secret values in logs.

## 7. Deployment and troubleshooting
Useful commands:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get deployments,services,ingress
kubectl rollout status deployment/order-api
kubectl rollout undo deployment/order-api
```

Check events, readiness, image pull errors, resource limits, logs, service selectors, DNS, and network policy. A running Pod is not necessarily ready to serve requests.

## 8. Common mistakes
- Using Pod IPs as permanent endpoints.
- Missing readiness probes.
- Setting memory limits below realistic JVM needs.
- Storing secrets in image or ConfigMap.
- Scaling app replicas without checking database capacity.
- Assuming HPA can scale without metrics and correct resource requests.

## 9. Interview questions
**Q1. Pod vs Deployment?** Pod runs containers; Deployment manages replicas and rollout of Pods.

**Q2. Service vs Ingress?** Service provides stable in-cluster networking; Ingress routes external HTTP traffic through a controller.

**Q3. Readiness vs liveness?** Readiness controls whether traffic should be sent; liveness indicates whether the container should be restarted.

**Q4. Request vs limit?** Request is used for scheduling; limit caps resource consumption.

**Q5. Does HPA solve every scaling problem?** No. Database, queue, downstream services, and app state can still be bottlenecks.

## 10. Practice
1. Deploy a Spring Boot API with three replicas.
2. Explain what happens when readiness fails.
3. Diagnose CrashLoopBackOff and ImagePullBackOff.
4. Choose CPU/memory requests using observed metrics.
