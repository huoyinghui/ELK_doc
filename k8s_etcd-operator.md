##### imgfit 部署


##### 1.helm install imgfit
```bash
➜  apk_charts git:(imgfit) ✗ helm install ./imgfit --name imgfit
NAME:   imgfit
LAST DEPLOYED: Thu May 24 15:48:32 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME           DATA  AGE
imgfit-config  3     0s

==> v1/Service
NAME    TYPE       CLUSTER-IP    EXTERNAL-IP  PORT(S)            AGE
imgfit  ClusterIP  10.105.75.75  <none>       9000/TCP,8001/TCP  0s

==> v1/StatefulSet
NAME    DESIRED  CURRENT  AGE
imgfit  2        1        0s

==> v1beta2/EtcdCluster
NAME          AGE
etcd-cluster  0s

==> v1/Pod(related)
NAME      READY  STATUS             RESTARTS  AGE
imgfit-0  0/2    ContainerCreating  0         0s


NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=imgfit,release=imgfit" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://localhost:9000/v2/image/test/icon.png?w=100&fakeurl=1&type=.webpto resize your image"
  kubectl port-forward imgfit-1/0  8001:8001 9000:9000

➜  apk_charts git:(imgfit) ✗

```

##### 2.imgfit test
```bash


#### valuese rbac 必须打开， etcd-operator 才可以工作
```bash
E0524 08:25:14.106786       1 leaderelection.go:224] error retrieving resource lock default/etcd-operator: endpoints "etcd-operator" is forbidden: User "system:serviceaccount:default:imgfit-etcd-operator-etcd-operator" cannot get endpoints in the namespace "default"


time="2018-05-24T08:25:34Z" level=info msg="Event(v1.ObjectReference{Kind:"Endpoints", Namespace:"default", Name:"etcd-operator", UID:"0ce1a9b2-5e35-11e8-8e43-5254001a871c", APIVersion:"v1", ResourceVersion:"6617363", FieldPath:""}): type: 'Normal' reason: 'LeaderElection' imgfit-etcd-operator-etcd-operator-689bff8bc4-d5ljc became leader"
time="2018-05-24T08:25:35Z" level=info msg="creating cluster with Spec:" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="{" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="    "size": 1," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="    "baseImage": "quay.io/coreos/etcd"," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="    "version": "3.2.10"," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="    "pod": {" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="        "resources": {" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="            "limits": {" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="                "cpu": "100m"," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="                "memory": "128Mi"" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="            }," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="            "requests": {" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="                "cpu": "100m"," cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="                "memory": "128Mi"" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="            }" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="        }" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="    }" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:35Z" level=info msg="}" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:36Z" level=info msg="cluster created with seed member (etcd-cluster-0000)" cluster-name=etcd-cluster pkg=cluster
time="2018-05-24T08:25:36Z" level=info msg="start running..." cluster-name=etcd-cluster pkg=cluster
```

```bash
etcd-operator:
  # rbac:
  #   create: false
  cluster.enabled: true
  customResources:
    createEtcdClusterCRD: true

  deployments:
    # etcdOperator: false
    backupOperator: false
    restoreOperator: false
  
  etcdCluster:
    size: 1
    pod:
      resources:
        limits:
          cpu: 100m
          memory: 128Mi
        requests:
          cpu: 100m
          memory: 128Mi
      nodeSelector: {}
```
