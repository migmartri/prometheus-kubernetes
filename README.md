# prometheus-kubernetes
# TODO

==== Create gcepersistentdisk

```
gcloud compute disks create --size 20GB grafana-data
gcloud compute disks create --size 50GB --type pd-ssd prometheus-data
```

==== Caveats

Since we are using gcepersistentdisk, the number of pods that can mount
that volume type is limited to 1. That means that the number of replicas
for prom-server replication controller needs to be fixed to 1.

This also affects the ability of executing rolling-updates on that rc.

TODO: Explore other persistence layer options.

==== Deploy

TODO
