# prometheus-kubernetes

Prometheus setup that contains:

- One instance of prometheus server configured to scrap metrics from a prometheus push gateway service.
- Prometheus push gateway service used for receiving short lived jobs.
- Grafana dashboard


### Deploy

* Create gcepersistentdisk

```
gcloud compute disks create --size 20GB grafana-data
gcloud compute disks create --size 50GB --type pd-ssd prometheus-data
```

* Build prom-server image and push it into a container registry


```
cd prom-server
docker build -t prom-server .

# Example using gcr
docker tag prom-server gcr.io/miguel-test/prom-server
docker push gcr.io/miguel-test/prom-server
```

* Update prometheus-server-controller.yml to use that new image.
* Deploy setup into k8s.

```
# From the project root execute
kubectl create -f prom-server
kubectl create -f prom-gateway
kubectl create -f prom-grafana
```

* Configure Grafana

If you are using Google Container Engine, the grafana service should
have created an HTTP Load balancer exposed via a public IP address.

You can find the IP address executing:

```
kubectl describe services grafana | grep LoadBalancer

```

Once inside, create a data source using the endpoint:

```
URL: http://prom-server:9090/
Access: Proxy
```

### Caveats

Since we are using gcepersistentdisk, the number of pods that can mount
that volume type is limited to 1. That means that the number of replicas
for prom-server replication controller needs to be fixed to 1.

This also affects the ability of executing rolling-updates on that rc.

TODO: Explore other persistence layer options.
