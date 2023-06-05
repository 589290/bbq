# Thanos - HA Prometheus with long term data retention 

## Synopsis
The current monitoring deployment via Prometheus does not support long term storage of data for as well as the high availability of Prometheus in Kubernetes. To resolve this issue we use [Thanos](https://thanos.io) which has several components:
- **SideCar** runs side-by-side of each Prometheus instance. It saves Prometheus data every 2 hours to storage (an S3 bucket in our case) and also serves realtime metrics that are not saved to storage.
- **Store** serves saved metric data from (S3) storage.
- **Querier** handles the Prometheus query API and has a UI similar to that of Prometheus. It queries **Store** and **Sidecar** to return the relevant metrics. If there are multiple Prometheus instances configured for HA, it also deduplicates metric data.

## Deployment
- Create an s3 bucket in your enviroment
- Update the value file of monitoring's chart with thanos sidecar

```
prometheusSpec:
  thanos:
    baseImage: registry1.dso.mil/ironbank/opensource/thanos/thanos
    version: v0.29.0
    objstoreConfig:
      key: objstore.yml
      name: thanos-objstore-secret

# the secret `thanos-objstore-secret` stores information regarding bucket configuration which comes from thanos' chart 
```

-  Install [thanos helm chart](https://repo1.dso.mil/platform-one/big-bang/apps/sandbox/thanos.git) changing the value file according to your specific requirements. Below is a minimal value.yaml file for Thanos:

```
objstoreConfig:
  type: S3
  config:
    bucket: xzz
    endpoint: xxx
    access_key: xxx
    secret_key: xxx
    insecure: true
stores:
  - dnssrv+_grpc._tcp.prometheus-operated.monitoring.svc.cluster.local
```

 - Ensure that all the services are running with prometheus, specifically: store and querier.
 - You can add the query service as the datasource to the grafana for long term data projection.

## Summary
This will provide high available P rometheus with long term data retention. Please set the redention period of prometheus to 24 hr since every two hr thanos side car will store data in s3. You can manage S3 retension by your organization policy.


## Related
```
docker pull registry1.dso.mil/ironbank/opensource/thanos/thanos:v0.29.0
```
