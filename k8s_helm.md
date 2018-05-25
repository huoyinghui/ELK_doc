#### helm
[helm官方教程](https://docs.helm.sh/chart_template_guide/#getting-started-with-a-chart-template)

[helm 镜像](https://hub.kubeapps.com/)

[helm 源码](https://github.com/kubernetes/helm)

[chart 仓库](https://github.com/kubernetes/charts)

helm常用命令:

```bash
helm create demo
helm install --debug --dry-run  ./demo --name demo
helm install ./demo --name demo
helm ls
helm upgrade demo ./demo -f ./values.yaml
helm del --purge demo
helm dependency build .
```


[处理依赖](https://docs.helm.sh/helm/#helm-dependency)

某服务依赖于etcd:
```bash
➜  demo git:(9-demo) cat requirements.yaml
dependencies:
  - name: etcd-operator
    version: "0.7.7"
    repository: "@stable"
➜  imgfit git:(9-demo)
➜  imgfit git:(9-demo) ✗ ll ./chart
➜  imgfit git:(9-demo) ✗ ll
total 40
drwxr-xr-x  10 apple  staff   340  5 25 15:26 .
drwxr-xr-x  21 apple  staff   714  5 25 12:29 ..
-rw-r--r--   1 apple  staff   333  5 25 11:27 .helmignore
-rw-r--r--   1 apple  staff   102  5 25 11:27 Chart.yaml
drwxr-xr-x   2 apple  staff    68  5 25 15:31 charts
drwxr-xr-x   5 apple  staff   170  5 25 11:27 files
-rw-r--r--   1 apple  staff   243  5 25 11:27 requirements.lock
-rw-r--r--   1 apple  staff    85  5 25 11:27 requirements.yaml
drwxr-xr-x   8 apple  staff   272  5 25 11:27 templates
-rw-r--r--   1 apple  staff  1682  5 25 11:27 values.yaml
➜  imgfit git:(9-demo) ✗ ll ./charts
total 0
drwxr-xr-x   2 apple  staff   68  5 25 15:31 .
drwxr-xr-x  10 apple  staff  340  5 25 15:26 ..
➜  imgfit git:(9-demo) ✗ helm dependency build .
Hang tight while we grab the latest from your chart repositories...
...Unable to get an update from the "local" chart repository (http://127.0.0.1:8879/charts):
	Get http://127.0.0.1:8879/charts/index.yaml: dial tcp 127.0.0.1:8879: connect: connection refused
...Successfully got an update from the "incubator" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 1 charts
Downloading etcd-operator from repo https://kubernetes-charts.storage.googleapis.com
Deleting outdated charts
➜  imgfit git:(9-demo) ll ./charts
total 16
drwxr-xr-x   3 apple  staff   102  5 25 15:31 .
drwxr-xr-x  10 apple  staff   340  5 25 15:31 ..
-rw-r--r--   1 apple  staff  7387  5 25 15:31 etcd-operator-0.7.7.tgz
➜  imgfit git:(9-demo)
```

etcd-operator:
```bash
# configmap.yaml 引用: 
# {{ $etcdOperator := index .Values "etcd-operator" -}}
# endpoint = "http://{{ $etcdOperator.etcdCluster.name }}-client:2379"
# 需要index 获取变量, index .Values "etcd-operator" 
etcd-operator:
  cluster.enabled: true
  customResources:
    createEtcdClusterCRD: true

  deployments:
    # etcdOperator: false
    backupOperator: false
    restoreOperator: false
  
  etcdCluster:
    #etcd cluster name, default "etcd-cluster"
    name: "demo-etcd-cluster"
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







helm help

```bash
➜  imgfit git:(9-demo) helm
The Kubernetes package manager

To begin working with Helm, run the 'helm init' command:

	$ helm init

This will install Tiller to your running Kubernetes cluster.
It will also set up any necessary local configuration.

Common actions from this point include:

- helm search:    search for charts
- helm fetch:     download a chart to your local directory to view
- helm install:   upload the chart to Kubernetes
- helm list:      list releases of charts

Environment:
  $HELM_HOME          set an alternative location for Helm files. By default, these are stored in ~/.helm
  $HELM_HOST          set an alternative Tiller host. The format is host:port
  $HELM_NO_PLUGINS    disable plugins. Set HELM_NO_PLUGINS=1 to disable plugins.
  $TILLER_NAMESPACE   set an alternative Tiller namespace (default "kube-system")
  $KUBECONFIG         set an alternative Kubernetes configuration file (default "~/.kube/config")

Usage:
  helm [command]

Available Commands:
  completion  Generate autocompletions script for the specified shell (bash or zsh)
  create      create a new chart with the given name
  delete      given a release name, delete the release from Kubernetes
  dependency  manage a chart's dependencies
  fetch       download a chart from a repository and (optionally) unpack it in local directory
  get         download a named release
  history     fetch release history
  home        displays the location of HELM_HOME
  init        initialize Helm on both client and server
  inspect     inspect a chart
  install     install a chart archive
  lint        examines a chart for possible issues
  list        list releases
  package     package a chart directory into a chart archive
  plugin      add, list, or remove Helm plugins
  repo        add, list, remove, update, and index chart repositories
  reset       uninstalls Tiller from a cluster
  rollback    roll back a release to a previous revision
  search      search for a keyword in charts
  serve       start a local http web server
  status      displays the status of the named release
  template    locally render templates
  test        test a release
  upgrade     upgrade a release
  verify      verify that a chart at the given path has been signed and is valid
  version     print the client/server version information

Flags:
      --debug                           enable verbose output
  -h, --help                            help for helm
      --home string                     location of your Helm config. Overrides $HELM_HOME (default "/Users/apple/.helm")
      --host string                     address of Tiller. Overrides $HELM_HOST
      --kube-context string             name of the kubeconfig context to use
      --tiller-connection-timeout int   the duration (in seconds) Helm will wait to establish a connection to tiller (default 300)
      --tiller-namespace string         namespace of Tiller (default "kube-system")

Use "helm [command] --help" for more information about a command
```


##### helm 使用流程

##### helm 处理依赖
