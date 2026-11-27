Team members:
    Hadji Youcef Houssem Eddine
    Aidi Youcef
    Zernouh Lakhdar


# TP4 Answers

## Exercise 1

- **CoreDNS:** It is needed for DNS inside the cluster. Without it, pods cannot resolve service names.
- **kube-apiserver:** It is the main access point to the Kubernetes API. Without it, we cannot manage the cluster.
- **kube-scheduler:** It chooses a node for pods that are waiting to be scheduled. Without it, new pods may stay Pending.

## Exercise 2

(a) A Service looks for pods using its selector and the labels on the pods. Kubernetes then adds the matching pod addresses to the EndpointSlices, and the Service sends traffic to these endpoints.

(b) We changed the selector to `app: scoreboard-v2`, but the pod still has the label `app: scoreboard`. So the selector does not match any pod and the EndpointSlice has no endpoint.

## Exercise 3

(a) After changing the database path, the new pod was **0/1 READY** and had **0 restarts** in the screenshot. The old pod kept running and serving requests because the Deployment waits for the new pod to become ready before removing the old one.

(b) After changing the liveness probe to `/readyz`, the pod had **4 restarts** after about two minutes, as shown in my screenshot.

(c) The **readinessProbe** checks `/readyz` and tells Kubernetes if the pod should receive traffic. The **livenessProbe** checks `/healthz` and tells Kubernetes if the container needs to be restarted. The **startupProbe** also checks `/healthz` during startup. When I changed liveness to `/readyz`, the database problem made the liveness check fail, so Kubernetes kept restarting the container.

(d) The database is down, so the application cannot become ready. When liveness also checks readiness, Kubernetes restarts the application again and again. This makes the problem worse instead of letting the application stay running while it is not ready.

## Exercise 4

(a) The smoke test passed because the old pod was still running and answering requests. The new pod was **0/1 READY** and had the status **ErrImagePull** because the image did not exist. The Deployment uses a rolling update, so it kept the old pod while the new one was not ready.

(b) `kubectl rollout undo` creates a new revision in the Deployment's rollout history. It does not change the file in Git.

(c) The file `k8s/deployment.yaml` still contains the original working image from TP3. The `set image` and `rollout undo` commands changed the cluster directly, not the YAML file.

## Exercise 5

(a) A colleague who only reads the repository cannot see any of the four changes: `set env`, `patch`, `set image`, and `scale`. These changes were made directly in the cluster and were not saved in the files.

(b) **GitOps** can solve this problem. The configuration is kept in Git, and a tool such as Argo CD or Flux checks the cluster and applies the changes needed to make it match the files.

## Screenshots

All screenshots are in the provided archive:

- `exo1.png`: nodes status
- `exo1p3s.png`: pods in the cluster
- `exo2p1.png`: namespaces
- `exo2p2.png`: image details
- `exo2p3.png`: pod status
- `exo2-N.png`: EndpointSlice with the service endpoint
- `exo2testfail.png` and `exo2testfail.png.png`: smoke test failed after changing the selector
- `exo2testsmok.png`: smoke test passed after restoring the selector
- `exo3p1.png`: pod readiness and restarts after changing the database path
- `exo3p2.png`: EndpointSlice conditions
- `exo3timFail.png`: restart count after changing liveness to `/readyz`
- `exo4p1.png`: pod status during the bad release (`ErrImagePull`)
- `exo4testSmoke.png`: smoke test passed during the bad release
- `exo4revsionlist.png`: rollout history
- `exo4p4.png`: rollout status after rollback
- `exo5p1.png`: `kubectl diff` showing the difference in replicas
