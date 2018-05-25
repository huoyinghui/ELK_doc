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

1.尽可能使用变量，动态配置.(比如依赖部署， todo)

2.尽可能固定参数，应为每个pod都独立运行ip不同，所以httport/grpcport 应当固定. (http://sername:port 对其他调用者友好)


自顶向下: ingress 把浏览器请求代理到svc, svc通过endpoint， 负载均衡到具体的pod
1.  ingress (域名访问)
2.  svc     (ingress 转发到 svc)
3.  pod1, pod2...podn (svc endpoint 负载均衡)


建议调试顺序: pod->svc->ingres

kubectl port-forward pod-name 8000:8000

集群外访问pod:

curl -v localhost:8000

集群内访问svc:

curl -v servername:8000

域名直接访问

###### 2.1 helm 创建项目
```bash
helm create fs_router
```

###### 2.2 helm 项目基本依赖:
1.Chart.yaml
```bash
基本采用自动生成， 需要确认版本
```

2.Valuse.yaml

如果服务不需要ingress, 只要配置镜像和端口即可
```bash
replicaCount: 1

image:
  repository: docker.hub.xxx/server/fs-router
  tag: "1.0.1"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

resources: {}

nodeSelector: {}

tolerations: []

affinity: {}
```

目前svc 已经配置，接下来配置app 启动需要的配置信息

###### 2.3 helm 项目基本configmap
path:./templates/configmap.yaml

1.抽象app.yml 中可配置参数, 固定端口等对其他服务有影响的变量

2.如果当前应用的配置名字使用了"_", 在Valuse定义变量，建议使用驼峰命名, 以免造成取值困难，如etcd-operator。

(个人感觉每个chart项目名称应该使用驼峰密码，这样被当作依赖时，可以直接使用。
命名中出现"-", go tmpl 会出现解析问题, 需要使用index 的方式获取变量)

```bash
{{- $fullName := include "fs-router.fullname" . -}}
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ $fullName }}-config
  labels:
    app: {{ template "fs-router.name" . }}
    chart: {{ template "fs-router.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
data: 
  app.yml: |
    app.yml: |
    app_name: {{ $fullName }}
    grpc_listen: :5004
    cache_size: {{ .Values.cacheSize }}
    # host: {{ .Values.host }} 不能解析为数据，解析的是go 对象
{{ toYaml .Values.host | indent 4 }}
    health_check_timeout: 60s
    health_scheme: http
    health_view: /health
    log_mod: {{ .Values.logLevel }}
    extra_hosts: {{ .Values.extraHosts }}
```

抽象变量如下: 
  Values:
    cacheSize: xxx
    host: []
    logLevel: xxx


###### 2.3.2 helm 根据configmap抽象变量，补充Valuse.yaml
```bash
cacheSize: 50
logLevel: 6
extraHosts:
- d-oppo.download.com
host: 
- name: OHV
  tags:
  - default
  host_conf:
  - replicate: 10
    hostname: d-01.download.com
  - replicate: 10
    hostname: d-02.download.com
  - replicate: 10
    hostname: d-03.download.com
  - replicate: 10
    hostname: d-04.download.com
  - replicate: 10
    hostname: d-05.download.com
  - replicate: 10
    hostname: d-06.download.com
- name: OHV2
  tags:
  - US
  host_conf:
  - replicate: 1
    hostname: d-01.download.com
```
[helm 模版语言](https://www.kubernetes.org.cn/3887.html)
Valuse: host 更新为变量
```bash
host: 
  host:
  - name: OHV
    tags:
    - default
    host_conf:
    - replicate: 10
      hostname: d-01.download.com
    - replicate: 10
      hostname: d-02.download.com
    - replicate: 10
      hostname: d-03.download.com
    - replicate: 10
      hostname: d-04.download.com
    - replicate: 10
      hostname: d-05.download.com
    - replicate: 10
      hostname: d-06.download.com
  - name: OHV2
    tags:
    - US
    host_conf:
    - replicate: 1
```


###### 2.4 helm 项目deployment/statefulset
1.configmap checksum 

2.volumes: configmap 卷

3.containers: 

3.1 ports: 程序需要暴漏的端口

3.2 volumeMounts: 
      把configmap 卷挂载到容器
```bash
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ template "fs-router.fullname" . }}
  labels:
    app: {{ template "fs-router.name" . }}
    chart: {{ template "fs-router.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ template "fs-router.name" . }}
      release: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ template "fs-router.name" . }}
        release: {{ .Release.Name }}
      annotations:
        checksum/config: {{ include (print $.Template.BasePath "/configmap.yaml") . | sha256sum }}
    spec:
      volumes:
        - name: config-volume
          configMap:
            name: {{ template "fs-router.fullname" . }}-config
            items:
            - key: app.yml
              path: app.yml

      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          volumeMounts:
            - mountPath: /app/conf
              name: config-volume
          ports:
            - name: grpc
              containerPort: 5008
              protocol: TCP
            - name: http
              containerPort: 4008
              protocol: TCP
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

###### 2.5 helm 项目service.yaml
svc: 通过服务名，负载均衡到pod

所以需要指定, pod的端口
```bash
apiVersion: v1
kind: Service
metadata:
  name: {{ template "fs-router.fullname" . }}
  labels:
    app: {{ template "fs-router.name" . }}
    chart: {{ template "fs-router.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: 4008
      protocol: TCP
      name: http
  selector:
    app: {{ template "fs-router.name" . }}
    release: {{ .Release.Name }}
```

debug: 
```bash
kubectl get cm fs-router-config -o yaml
```

###### 2.6 helm 项目ingress.yaml

###### 2.7 helm 项目NOTES.txt

```bash```

[教程](https://www.kubernetes.org.cn/3884.html)

