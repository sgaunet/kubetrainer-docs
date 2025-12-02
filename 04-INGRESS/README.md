Create an ingress controller to expose the application to the outside world. The ingress controller will be used to route traffic to the correct service based on the hostname and path. The ingress controller will be created using the NGINX Ingress Controller.

> **Platform Note:** This tutorial works on both Linux and macOS.
> - **Linux:** Direct access via http://localhost:80
> - **Mac (Docker Desktop):** Use `kubectl port-forward` to access (explained below)

```bash
$ kubectl apply -f ingress.yaml
ingress.networking.k8s.io/http-echo-ingress created
```

You can see the ingress with:

```bash
$ kubectl get ingress
NAME        CLASS    HOSTS   ADDRESS   PORTS   AGE
http-echo   <none>   *                 80      1m
```

You can also see the ingress's details with:

```bash
$ kubectl describe ingress http-echo
Name:             http-echo
...
```

## Accessing the Application

### Method 1: Direct Access (Linux)

On Linux, the ingress is directly accessible via the port mapping configured in kind:

```bash
$ curl http://localhost:80
```

Or open [http://localhost:80](http://localhost:80) in your browser.

### Method 2: Port Forward (Mac/Windows)

On Mac and Windows with Docker Desktop, use kubectl port-forward:

```bash
$ kubectl port-forward --namespace ingress-nginx service/ingress-nginx-controller 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

**Keep this terminal running** and access the application at [http://localhost:8080](http://localhost:8080)

Test with curl in a new terminal:
```bash
$ curl http://localhost:8080
```

**Why port-forward on Mac?**
Docker Desktop runs in a VM, so the kind port mappings don't directly expose to macOS.
The `kubectl port-forward` command bridges this gap and is a valuable technique for accessing
any Kubernetes service during development.

**Note:** Port-forward also works on Linux as an alternative to direct access.

## Quick Commands with Task

If you have [task](https://taskfile.dev/) installed, you can use these shortcuts:

```bash
# Apply the ingress
$ task apply

# Start port forwarding (Mac/Windows)
$ task port-forward

# In another terminal - test access
$ task test-mac          # If using port-forward
$ task test-linux        # If on Linux with direct access

# Check status
$ task status

# View controller logs
$ task logs
```

## Troubleshooting

### "Connection refused" at http://localhost:80 (Mac)

This is expected on Docker Desktop. Use the port-forward method above.

### Verify Ingress Controller is Running

```bash
$ kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-xxxxxxxxxx-xxxxx   1/1     Running   0          5m
```

### Check Ingress Controller Service

```bash
$ kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.x.x       <pending>     80:xxxxx/TCP,443:xxxxx/TCP   5m
```

The EXTERNAL-IP showing `<pending>` is normal for kind clusters.

### Verify Ingress Resource

```bash
$ kubectl describe ingress http-echo
Name:             http-echo
Namespace:        default
Address:
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /   http-echo:8080 (10.244.x.x:8080)
```

Check that the backend shows your service and pod IP.
