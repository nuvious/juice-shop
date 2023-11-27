# Juice-Shop Kubernetes

This is a simple kubernetes configuration for the [Juice-Shop](https://owasp.org/www-project-juice-shop/) hacking
challenge. This particular deployment has an [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
definition as well as a network policy that can be used to restrict access to private IP ranges.

## Quick-Start

Create an overlay directory and initial `kustomization.yaml` file. Replace `juice-shop.local` with whatever hostname
you want the ingress to bind to.

```bash
mkdir -p overlays/my-overlay
cat > overlays/my-overlay/kustomization.yaml << EOF
resources:
  - ../../base
patches:
  - target:
      kind: Ingress
    patch: |-
      - op: replace
        path: /spec/rules/0/host
        value: juice-shop.local
EOF
```

Then launch the deployment and monitor its progress:

```bash
kubectl apply -k overlays/my-overlay && \
    watch kubectl get all -n juice-shop
```

You should eventually see output similar to below:

```bash
Every 2.0s: kubectl get all -n juice-shop                                                                                                rpiterm: Sun Nov 26 23:07:45 2023

NAME                              READY   STATUS    RESTARTS   AGE
pod/juice-shop-6dc6b764d4-dvjkd   1/1     Running   0          6m4s

NAME                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/juice-shop-service   ClusterIP   10.152.183.241   <none>        3000/TCP   6m4s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/juice-shop   1/1     1            1           6m4s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/juice-shop-6dc6b764d4   1         1         1       6m4s
```

Once the pod is up you can navigate to your specified hostname. This may require modifying your DNS settings in your
network/router and/or modifying `/etc/hosts` or `C:\Windows\System32\drivers\etc\hosts` to have an entry similar to the
below:

```plaintext
juice-shop.local [IP OF INGRESS NODE]
```
