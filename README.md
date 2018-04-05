# HBase chart

A chart to deploy Hbase with Hadoop using Kubernetes. Heavily inspired by the [Hadoop chart](https://github.com/kubernetes/charts/tree/master/stable/hadoop).

## How-to contribute

This chart was developed without former k8s experience, please help us improve the chart by contributing to it!

## Getting started

You need:

* Kubernetes 1.8
* [Helm](https://helm.sh/)

```bash
helm install --name myzk incubator/zookeeper --set servers=1,heap="1G"
helm del --purge hbase;helm install . --name hbase
```

## Architecture

This chart is using several functionalities from Kubernetes.

* [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/): at first, it is used as key-value to store elements. Here, we are using it to store config files. Furthermore, we are using it to inject a boostrap.sh to start our container.

  * For every container, we are mouting a container in /tmp based on the content of the ConfigMap (one entry == one file).
  * Entrypoint for every component is the bash called `bootstrap.sh`, which is hold by the ConfigMap.
  * `bootstrap.sh` is copying the files in the ConfigMap to the right location, starting the daemon and tail the logs

* [Headless Service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services): In Kubernetes, every request to a pod a loadbalanced through a proxy by default. But Hbase is directly trying to connect to the RS, so by enabling headless mode, we can directly access the RS container.

* [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/): it is used to deploy stateful applications to Kubernetes.

* [PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/): Allow user to define policy on pod failure.

* [PersistentVolumClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/): allows pod to have volumes for data. Used for HDFS.

There's a YAML per role and per functionality. Binding is done through [Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).

## TODO

* Namenode HA
* Hbase Master HA
* Include Zookeeper into the Charts