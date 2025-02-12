# Storage Usage

In this example, you will learn how to use the Storage.

## Install Operator

Follow [Operator installation instrument](../../README.md#operator) to install the operator.

## Define Storage with default setting

1. sample.yaml(use the internal type)
```yaml
apiVersion: operator.skywalking.apache.org/v1alpha1
kind: Storage
metadata:
  name: sample
spec:
  type: elasticsearch
  connectType: internal
  version: 7.5.1
  instances: 3
  image: docker.elastic.co/elasticsearch/elasticsearch:7.5.1
  security:
    user:
      secretName: default
    tls: true
```
2. sample.yaml(use the external type)
```yaml
apiVersion: operator.skywalking.apache.org/v1alpha1
kind: Storage
metadata:
  name: sample
spec:
  type: elasticsearch
  connectType: external
  address: "https://elasticsearch"
  security:
    user:
      secretName: default
```

## Deploy Storage

1. Deploy the Storage use the below command:

```shell
$ kubectl apply -f sample.yaml 
```

2. Check the Storage in Kubernetes:

* If you deploy the storage with the internal type:

```shell
$ kubectl get storage
NAME     INSTANCES      TYPE             VERSION   CONNECTTYPE
sample   3              elasticsearch   7.5.1     internal
```

* If you deploy the storage with the external type:

```shell
$ kubectl get storage
NAME     INSTANCES      TYPE             VERSION   CONNECTTYPE
sample                  elasticsearch   7.5.1     external
```

3. Check the Statefulset in Kubernetes:

```shell
$ kubectl get statefulset   
NAME                    READY   AGE
sample-elasticsearch   3/3     7s
```

## Specify Storage Name in OAP server

Here we modify the [default OAP server configuration file](../../operator/config/samples/default.yaml),the new yaml file as follows:

```yaml
apiVersion: operator.skywalking.apache.org/v1alpha1
kind: OAPServer
metadata:
  name: default
spec:
  version: 9.2.0
  instances: 1
  image: apache/skywalking-oap-server:9.2.0
  service:
    template:
      type: ClusterIP
  storage:
    name: sample
```

1. Deploy the OAP server use the new yaml file:

```shell
$ kubectl apply -f oap.yaml 
```

2. Check the OAP server in Kubernetes:

```shell
$ kubectl get oapserver 
NAME     INSTANCES   RUNNING   ADDRESS
sample   1           1         sample-oap.default
```

3. Check whether the pod generated by OAP server is running correctly. 

```shell
$ kubectl get pod -l app=oap
NAME                          READY   STATUS    RESTARTS   AGE
sample-oap-5bc79567b7-tkw6q   1/1     Running   0          6m31s
```
