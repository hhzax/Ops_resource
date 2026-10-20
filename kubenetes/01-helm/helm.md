

# helm 安装

```shell
网址：
https://github.com/helm/helm/releases ---》 版本3.15.2  ---》 linux amd64 --》 安装包就下载下来了
传入 linux 系统中 ----》使用下面的命令

tar xf tar xf helm-v3.15.4-linux-amd64.tar.gz

cd linux-amd64/

mv helm /usr/local/bin

chmod +x /usr/local/bin/helm

helm version

whixc helm


安装： version
```

说明

```shell
对于复杂的应用来说，涉及到相关的yaml文件非常的多
把它都组织到一起形成一个chart包，然后用helm统一进行安装与卸载会非常方便

如何使用
v2  ---- 》  tiller 服务进行通信
v3  ---- 》   kubernetes CRD 资源来进行管理，是需要连接上 kubernetes 即可使用 helm

chart 仓库里放着 chart 包    
helm  放着 chart 仓库
k8s 集群从 helm 里面会找到 chart 仓库，然后找到 chart 包 

核心概念
1、Chart包仓库(Repository)
[root@k8s-master-01 helm]# helm repo list
NAME  	URL                                      
stable	http://mirror.azure.cn/kubernetes/charts/

2、仓库里放到的是chat包
[root@k8s-master-01 helm]# helm search repo stable
NAME                                 	CHART VERSION	  APP VERSION            	DESCRIPTION                                       
stable/acs-engine-autoscaler         	2.2.2        	  2.1.1                  	DEPRECATED Scales worker nodes within agent pools 


3、Release：
			每安装或升级一次chart包都会得到 一条relrease记录
```

## 使用

### 添加 repo仓库

```shell
helm repo add stable http://mirror.azure.cn/kubernetes/charts/

helm repo list
NAME  	URL                                      
stable	http://mirror.azure.cn/kubernetes/charts/

查看稳定版的chart包
helm search repo stable

# 不指定版本，默认下载最新；只下载安装包
helm pull stable/prometheus-rabbitmq-exporter --version 0.1.1


helm repo update  重新拉取index.yaml
```

### 安装 chart 包的方式

```shell
手动下载
http://mirror.azure.cn/kubernetes/charts/  下载后传入linux系统

helm命令下载
# 不指定版本，默认下载最新
helm pull stable/prometheus-rabbitmq-exporter --version 0.1.1
--untar  # 下载后并解压
--destination /path/to/dir  # 指定路径

[root@k8s-master-01 linux-amd64]# ll
总用量 20
-rw-r--r-- 1 root root  3559 10月 10 09:17 prometheus-rabbitmq-exporter-0.1.1.tgz

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo list
helm repo update
helm search repo prometheus | head -5 
helm search repo grafana/grafana --versions | head -5
helm show chart grafana/grafana > values-orig.yaml
```

## 安装 chart 实例

```shell
通过官方的chart仓库安装
helm install 起的release名  stable/mariadb --version 1.1  #从官方在stable库中拿到的chart包安装，如果不指定版本则默认最新

通过本地chart压缩包
helm install 起的release名 ./ngin-1.26/tgz  #已经将tgz压缩包下载到本地了，就不用指定版本了

通过解压后的chart目录
helm install 起的release名 path/to/foo  #install后的名字foo是你指定的release名字

通过完整url
helm install 起的release名 http://mirror.azure.cn/kubernetes/charts/mariadb-7.3.9.tgz

参数：
--namespace  # release的命名空间 
 
-dry-run     #模拟一次安装，常和--debug一起使用，调试charty模板是否正常

--values     #使用yaml文件中指定值

完整版
```

### 安装一个 Grafana 测试

```shell
helm pull grafana/grafana
ls
grafana-10.5.15.tgz  linux-amd64  values-orig.yaml

tar tzf grafana-10.5.15.tgz | head

tar -xzf grafana-*.tgz

ls grafana

移除仓库
helm repo list
helm repo remove Grafana
```

#### 目录结构

```shell
[root@k8s-master-01 helm]# tree grafana
grafana
├── Chart.yaml
├── ci
│   ├── default-values.yaml
│   ├── with-affinity-values.yaml
│   ├── with-dashboard-json-values.yaml
│   ├── with-dashboard-values.yaml
│   ├── with-extraconfigmapmounts-values.yaml
│   ├── with-image-renderer-values.yaml
│   ├── with-nondefault-values.yaml
│   ├── with-persistence.yaml
│   └── with-sidecars-envvaluefrom-values.yaml
├── dashboards
│   └── custom-dashboard.json
├── README.md
├── templates
│   ├── clusterrolebinding.yaml
│   ├── clusterrole.yaml
│   ├── configmap-dashboard-provider.yaml
│   ├── configmap.yaml
│   ├── configSecret.yaml
│   ├── _config.tpl
│   ├── dashboards-json-configmap.yaml
│   ├── deployment.yaml
│   ├── extra-manifests.yaml
│   ├── headless-service.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── image-renderer-deployment.yaml
│   ├── image-renderer-hpa.yaml
│   ├── image-renderer-network-policy.yaml
│   ├── image-renderer-servicemonitor.yaml
│   ├── image-renderer-service.yaml
│   ├── ingress.yaml
│   ├── networkpolicy.yaml
│   ├── NOTES.txt
│   ├── poddisruptionbudget.yaml
│   ├── podsecuritypolicy.yaml
│   ├── _pod.tpl
│   ├── pvc.yaml
│   ├── rolebinding.yaml
│   ├── role.yaml
│   ├── route.yaml
│   ├── secret-env.yaml
│   ├── secret.yaml
│   ├── serviceaccount.yaml
│   ├── servicemonitor.yaml
│   ├── service.yaml
│   ├── statefulset.yaml
│   └── tests
│       ├── test-configmap.yaml
│       ├── test-podsecuritypolicy.yaml
│       ├── test-rolebinding.yaml
│       ├── test-role.yaml
│       ├── test-serviceaccount.yaml
│       └── test.yaml
└── values.yaml
```

## helm 四种安装来源

```shell
通过官方的chart仓库安装
这个安装下来的是tgz压缩包，需要自行解压缩
helm install 起的release名  stable/mariadb --version 1.1  #从官方在stable库中拿到的chart包安装，如果不指定版本则默认最新

通过本地chart压缩包 ，需要去官网拿到chart包，或者自建chart包
helm install 起的release名 ./ngin-1.26/tgz  #已经将tgz压缩包下载到本地了，就不用指定版本了

通过解压后的chart目录
helm install 起的release名 path/to/foo  #install后的名字foo是你指定的release名字

通过完整url
helm install 起的release名 http://mirror.azure.cn/kubernetes/charts/mariadb-7.3.9.tgz

安装通用参数
-n xxx --create-namespace 撞到指定命令空间
--dry-run   演练，不实装
--generate-name:懒得起名，让helm自动生成
--set key=value  / -f my.yaml 定制参数


完整实例：
	helm install egon-release-name ./mysql-1.6.9.tgz --namespace kube-system 

	helm install egon-release-name ./mysql-1.6.9.tgz --namespace kube-system --dry-run
	
	
	
了解：
	helm repo update # 类似于yum makecahe
		
	helm install --generate-name ./mysql-1.6.9.tgz --namespace kube-system
```

# chart 包管理及自建 chart 包

## 定制化安装 chart 包

```shell
查看参数值
helm show values chart包的来源/路径 # 本质查看的chart包下values.yaml的内容
helm show values stable/mysql
helm show values ./mysql-1.6.9.tgz  

定制化的参数	
在安装的时候用--set选项传入key=value，--set imageTag="5.8.0"
在安装的时候用-f 选项执行自己的my_values.yaml
chart包默认自带的: values.yaml（默认值）

可以进入目录里面更改数据
你没指定的参数沿用默认值，你指定的参数会优先覆盖生效。
```

定制化参数的三种形式

```shell
1、chart内置默认的values.yaml
可以使用 helm show values # tgz压缩包名查看
优先级：最低
特点：包含了所有可配置参数的默认值，开箱即用，用户无需从零编写全部参数。
作用：你只需要修改你这边差异项

2、自定义values.yaml
用户自己编写的 YAML 配置文件，安装 / 升级时通过 `-f`（或 `--values`）参数传入。
使用方式：
helm install exporter ./chart.tgz -f base.yaml -f prod.yaml

优先级：高于内置默认值，低于命令行参数。
特点：
     1、只写需要修改的字段即可，没写的字段自动继承默认值，不需要全量复制
     2、可复用、可版本管理，适合多环境差异化配置。
     3、支持同时指定多个文件，命令行中越靠后的文件优先级越高，依次覆盖前一个文件的同名配置。
3、命令行动态参数
安装 / 升级命令中，通过 `--set` 系列参数直接传入的键值对。
优先级：最高，会强制覆盖前两层的所有同名配置。
使用方式：
helm install exporter ./chart.tgz -f custom.yaml \
  --set rabbitmq.password=临时测试密码
--set：普通键值对，自动推断数据类型（数字、布尔值、字符串）。
--set-string：强制将值作为字符串类型，避免纯数字密码、版本号被自动转成数值类型。
--set-json：传入 JSON 格式的复杂结构，适合嵌套较深的配置。

helm install egon-release-name ./mysql --namespace kube-system -f /a/b/c/my_values.yaml --dry-run
```

values 的 yaml 定制

```shell

```

helm 部署 mysql &&定制 values 参数值

## 自制 chart 包

repo 仓库操作

```shell
# 1. 看现状（空的）
helm repo list                          # → Error: no repositories to show（正常）

# 2. 添加两个活跃仓库
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana  https://grafana.github.io/helm-charts
helm repo list                          # → 两行：grafana / prometheus-community

# 3. 踩坑：不 update 直接搜（体会一下为什么需要 update）
helm search repo grafana                # 搜不到或不对 → 引出下一步

# 4. 刷新缓存（= yum makecache）
helm repo update                        # → Successfully got an update ... Happy Helming

# 5. 搜索 + 看多版本
helm search repo grafana | head -5
helm search repo grafana/grafana --versions | head -5    # 注意格式：仓库名/chart名

# 6. 看货不买
helm show chart grafana/grafana         # → 对方的 Chart.yaml
helm show values grafana/grafana | head -50              # → 对方的 values.yaml

# 7. 下载并解剖（呼应 chart 包格式）
helm pull grafana/grafana
tar -tzf grafana-*.tgz | head -10
tar -xzf grafana-*.tgz && ls grafana/   # → Chart.yaml values.yaml templates charts crds ...


 能说出 add 记地址、update 拉目录清单index.yaml、pull 只下载不安装。
```

从零造一个 chart

```shell
cd ~/k8s/helm

# 1. 先看看官方脚手架长什么样，然后删掉自己写
helm create scaffold
[root@k8s-master-01 chart]# ls scaffold/ scaffold/templates/   认脸：Chart.yaml/values.yaml/.helmignore/charts/templates
scaffold/:
charts  Chart.yaml  templates  values.yaml

scaffold/templates/:
deployment.yaml  _helpers.tpl  hpa.yaml  ingress.yaml  NOTES.txt  serviceaccount.yaml  service.yaml  tests
# 看完即弃，我们从零手写,删掉我们自行搭建 chart 包
rm -rf scaffold
```

**建 6 个文件**（内容照抄，抄完看注释）：

```shell
mkdir -p mychart/templates

1、
vim mychart/Chart.yaml
apiVersion: v2          # Helm3 用 v2
name: mychart           # chart 名，资源名会用到
description: 我从零写的第一个chart
type: application       # application（可安装）/ library（见后续）
version: 0.1.0          # chart 自己的版本，改模板就要升它
appVersion: "1.0.0"     # 里面应用的版本，仅展示

2、
vim mychart/values.yaml：
replicaCount: 1
image:
  repository: nginx
  tag: "1.25"
service:
  port: 80
config:
  env: dev
  features:
    - metrics
    - tracing
    
3、mychart/templates/_helpers.tpl（下划线开头=不产出资源，只放模板片段）：
vim mychart/templates/_helpers.tpl
{{- define "mychart.name" -}}
{{- printf "%s-%s" .Release.Name .Chart.Name | trunc 63 | trimSuffix "-" -}}
{{- end -}}

printf "%s-%s" A B	格式化拼接 → A-B	就是 C 的 printf
.Release.Name	本次 release 的名字	你在命令行起的：helm template demo ... 里的 demo
.Chart.Name	chart 的名字	Chart.yaml 里的 name: mychart 字段
trunc 63	截断到最长 63 字符	K8s 资源名的硬性上限
trimSuffix "-"	去掉结尾的 -	万一截断后以 - 收尾，K8s 不允许

4、
vim mychart/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.name" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
        - name: web
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
5、
vim mychart/templates/service.yaml：
apiVersion: v1
kind: Service
metadata:
  name: {{ include "mychart.name" . }}
spec:
  selector:
    app: {{ .Chart.Name }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.port }}

6、
vim mychart/templates/configmap.yaml（模板函数演示：upper/quote/range/toYaml）
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "mychart.name" . }}-config
data:
  env: {{ .Values.config.env | upper | quote }}
  features: |
    {{- range .Values.config.features }}
    - {{ . }}
    {{- end }}

这两个是管道函数——| 管道你用 grep 时已经熟了，Helm 里同理，值像水流一样从左往右流过每个函数：
text
{{ .Values.config.env | upper | quote }}
   "dev"（values里的值）→ upper → "DEV" → quote → "\"DEV\""

渲染验收
helm template demo ./mychart
验收：输出里能找到 name: demo-mychart（release名-chart名拼接）、image: "nginx:1.25"、env: "DEV"（upper 生效）、features 两行列表（range 生效）。
```

## 四种安装来源 + 部署验证测试

```shell
cd ~/k8s/helm

# 来源1：本地目录（开发姿势）
helm install from-dir ./mychart -n four-ways --create-namespace

# 来源2：本地 tgz
helm package mychart                              # → mychart-0.1.0.tgz（chart名-版本.tgz）
helm install from-tgz ./mychart-0.1.0.tgz -n four-ways

# 来源3：URL 远程 tgz（起个静态服务）
python3 -m http.server 8899 &                     # 当前目录=网站根目录
helm install from-url http://localhost:8899/mychart-0.1.0.tgz -n four-ways

# 来源4：仓库名/chart名（顺便搭出私有仓库雏形=第18集预告）
helm repo index . --url http://localhost:8899     # 生成 index.yaml，HTTP目录→chart仓库！
helm repo add myrepo http://localhost:8899
helm repo update
helm search repo mychart                          # 搜到自己的 chart
helm install from-repo myrepo/mychart -n four-ways


安装python
yum install -y python3
python3 --version        # 确认装好了，比如 3.6.8



清理
```

验收

```shell
验收
helm list -n four-ways
# ✅ 预期四条：from-dir / from-tgz / from-url / from-repo，CHART 列全是 mychart-0.1.0
# → 证明：来源只影响"货从哪来"，不影响装出来的东西

kubectl get all,cm -n four-ways        # 资源名前缀 from-dir-mychart 等，四套并存

kubectl port-forward -n four-ways svc/from-dir-mychart 8080:80
# 另开终端 curl -I http://localhost:8080  → HTTP/1.1 200 OK
```

清理

```shell
清理
helm uninstall from-dir from-tgz from-url from-repo -n four-ways
kubectl delete ns four-ways
kill %1
```

 此模块延伸的问题：

```shell
改模板调试用那种来源？
使用本地目录的那种chart包

没外网的机房如何装
使用tgz整过去
使用本地导出然后导入到机房里面
```



## 定制化参数三种与优先级

```shell
cd ~/k8s/helm

# 1级：默认值
helm template t ./mychart | grep image:            # → nginx:1.25

# 2级：-f 文件覆盖默认值 新增文件在执行的时候使用-f选项
cat > my-values.yaml << 'EOF'
image:
  tag: "1.27"
EOF
helm template t ./mychart -f my-values.yaml | grep image:     # → nginx:1.27

-f my-values.yaml（全写 --values）= 在安装/渲染/升级时，额外喂一份参数文件给 Helm。

机制是深度合并：chart 自带的 values.yaml 是默认值，你 -f 的文件里的同名键覆盖它，没提到的键保留默认。所以你的 my-values.yaml 只写了 image.tag: "1.27"，其他参数（replicaCount、service.port…）照样用默认值——这就是为什么定制 mysql 密码时只需要写三四行，不用抄整份 values。

优先级链（Phase 4 验证过的）：--set > -f 文件 > chart 自带 values.yaml。



# 3级：--set 覆盖一切  命令行
helm template t ./mychart -f my-values.yaml --set image.tag=9.9.9 | grep image:
# → nginx:9.9.9

验收：三次输出 1.25 → 1.27 → 9.9.9，优先级链亲眼见过。
```

## 升级与回滚

```shell
# 装一个专门用来折腾的
helm install app ./mychart -n updown --create-namespace
kubectl get deploy -n updown                        # 1/1

# 升级：扩到3副本 → 产生 revision 2
helm upgrade app ./mychart -n updown --set replicaCount=3
kubectl get deploy -n updown                        # 3/3
helm history app -n updown                          # 两条记录：1 和 2
[root@k8s-master-01 helm]# helm history app -n updown
REVISION	UPDATED                 	STATUS    	CHART       APP VERSION	DESCRIPTION     
1       	Mon Oct 12 19:31:53 2026	superseded	mychart-0.1.1.0.0      	Install complete
2       	Mon Oct 12 19:32:48 2026	deployed  	mychart-0.1.1.0.0      	Upgrade complete


# 回滚到 revision 1
helm rollback app 1 -n updown
kubectl get deploy -n updown                        # 回到 1/1

# 清理
helm uninstall app -n updown && kubectl delete ns updown


每次install/upgrade都会查嗯哼一条release记录，rollback按记录回退


验证测试
[root@k8s-master-01 helm]# helm history app -n updown
REVISION	UPDATED                 	STATUS    	CHART       APP VERSION	DESCRIPTION     
1       	Mon Oct 12 19:31:53 2026	superseded	mychart-0.1.1.0.0      	Install complete
2       	Mon Oct 12 19:32:48 2026	superseded	mychart-0.1.1.0.0      	Upgrade complete
3       	Mon Oct 12 19:33:41 2026	deployed  	mychart-0.1.1.0.0      	Rollback to 1   
[root@k8s-master-01 helm]# helm list -n updown
NAME	NAMESPACE	REVISION	UPDATED                               	STATUS  	CHART        	APP VERSION
app 	updown   	3       	2026-10-12 19:33:41.35731335 +0800 CST	deployed	mychart-0.1.0	1.0.0 
```

## 总自测清单（全过 = 第一遍流程毕业）

```shell
1、helm repo add 和 helm repo update 各自干什么？
    helm repo add 是添加 repo 仓库，不下载任何东西，只是在本地配置中加一笔仓库名 + URL链接
    helm repo update 更新已添加的 chart 仓库的index.yaml清单，为什么必须有 update？因为 search repo 搜的是本地缓存的这份清单

2、grafana/grafana 名字的前半截是什么？
    前半截grafana是chart仓库  后半截是chart包
    
3、chart 目录里 _helpers.tpl 的下划线什么含义？{{ include "mychart.name" . }} 的 . 是什么？
  Helm 渲染时会遍历 templates/ 下每个文件、把结果变成 K8s 资源；但以 _ 开头的文件被排除在渲染之外——它们唯一的使命是存放 define 的模板片段，供别的文件 include。（类比：.h 头文件不参与链接成可执行文件，只提供声明给别的编译单元 include。）如果去掉下划线叫 helpers.tpl，Helm 会试图渲染它——里面全是 define 片段，要么产出垃圾输出要么报错。

. 是什么：include 的第二个参数，把当前完整的上下文（root scope）传进命名模板，里面装着 .Values、.Chart、.Release 全套。没有它，模板函数内部就是个“瞎子”——取不到任何数据。

4、四种安装来源分别什么场景用？
    四种chart来源
      1、添加chart仓库后install chart包下载到本地，
      2、tgz导入到机器内使用   
      3、自建chart后的目录结构
      4、使用url链接下载chart包到本地
      本地目录一般用于开发调试
      本地tgz 离线环境
      URL版本锁定的分发
      仓库 
      
5、参数优先级顺序？helm package 生成的文件名由什么决定？
    低 chart values.yaml
    中 -f values
    高 命令行 --set key=values
    
  
6、helm history 里每次记录怎么产生的？
      每次使用install upgrade都会在history里面存下一次状态，类似于快照，可以通过rollback或者upgrade进行更新和回滚。
```

# chart 包内容详解

## chart 包目录讲解

```shell
[root@k8s-master-01 chart]# helm create chart1
Creating chart1
[root@k8s-master-01 chart]# ls
chart1
[root@k8s-master-01 chart]# tree chart1/
chart1/
├── Chart.yaml              # chart 自身的"身份证"
├── values.yaml             # 默认配置值，模板的数据源
├── charts/                 # 依赖的子 chart（新创建时是空的）
├── templates/
│   ├── _helpers.tpl        # 公共模板片段（_开头 = 不会被渲染成资源）
│   ├── deployment.yaml     # 模板化的 Deployment
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── serviceaccount.yaml
│   ├── hpa.yaml
│   ├── NOTES.txt           # 安装成功后打印给你的提示
│   └── tests/
│       └── test-connection.yaml   # helm test 用的连通性测试
├── crds/                   # （可选）CRD 放这里
└── .helmignore             # 打包时忽略哪些文件，同 .gitignore

3 directories, 10 files


helm install stable/mysql --generate-name --dry-run --debug

cd /root/.cache/helm/repository/

mv /root/.cache/helm/repository/mysql-1.6.9.tgz ./
[root@k8s-master-01 chart]# tar xfm mysql-1.6.9.tgz 
[root@k8s-master-01 chart]# ls
demo  mysql  mysql-1.6.9.tgz
```

### chart1/chart.yaml 内容详解

```shell
apiVersion: v2        # Helm 3 的格式版本，固定写 v2
name: mychart         # chart 名字
version: 0.1.0        # chart 包自己的版本（改模板就升它）
appVersion: "1.16.0"  # 包里那个应用的版本（换镜像才升它）

version 和 appVersion 是两码事：
    version 是“这个包”的版本号
    appVersion 是“包里装的软件”的版本号

[root@k8s-master-01 chart]# cat mysql/Chart.yaml 
apiVersion: v1
appVersion: 5.7.30
deprecated: true     # 为true则代表chart 已被弃用，不建议继续使用 (可选)
description: DEPRECATED -ast, reliable, scalable, and easy to use open-source relational
  database system.     #句话描述 (可选)
home: https://www.mysql.com/   #当前项目的 URL (可选)
icon: https://www.mysql.com/common/logos/logo-mysql-170x115.png
keywords:        # 搜索优化，搜索的关键字helm search repo mysql # 搜索带有mysql关键中的chart
- mysql
- database
- sql
name: mysql
sources:           # 当前项目源码 URL (可选)
- https://github.com/kubernetes/charts
- https://github.com/docker-library/mysql
version: 1.6.9
```

### chart1/values.yaml

```shell
values.yaml — 所有可变项集中在这一个文件。模板其他资源里用 {{ .Values.replicaCount }} 这样引用

它只是默认值，可以被覆盖，优先级从低到高：chart 自带的 values.yaml < -f 自定义文件 < --set 键值对
```

### chart1/charts/ 

```shell
放依赖的子 chart，每个子 chart 原样嵌进来。由 Chart.yaml 里的 dependencies: 声明，helm dependency update 负责拉取并生成 Chart.lock（锁定依赖的确切版本，防止每次拉的不一样）。类比 add_subdirectory() 挂进来的子项目。
```

### chart1/crds/ 

```shell
放 CRD，三条特殊规则：不模板化（写什么就是什么，不吃 values）、install 时先于一切资源创建、helm uninstall 不会删它（CRD 是集群全局的，删了会影响别的用户）。类比：用新类型前必须先向编译器声明类型，CRD 就是先向 K8s API 注册类型。
```

### chart1/template/

```shell
templates/ 核心中的核心。每个文件都是 Go template：K8s YAML 骨架 + {{ }} 留空处，渲染时 values 灌进去变成真 YAML。三条规则：
    文件名随意，一个文件里可以有多个资源（用 --- 分隔）；
    _ 开头的文件特殊：不会被渲染输出，只当素材库用；
    模板里常用的对象：.Values（配置值）、.Release（本次安装的信息，如 .Release.Name）、.Chart（读 Chart.yaml 的字段）。
```

#### chart1/templates/_helpers.tpl

```shell
就是那个 _ 开头的素材库，里面 define 了一些公共片段（比如生成资源全名的 mychart.fullname），别的模板去 include 它。完全对应 C++ 的 helper.h：被 include 的头文件不会单独编译出一个 .o，同理它不会单独渲染成一个 K8s 资源。
```

#### chart1/templates/NOTES.txt

```shell
也是一种模板，但渲染结果不发给 K8s，而是 install/upgrade 结束后在终端 print 出来，告诉你怎么访问服务（拿 NodePort、查密码之类）。相当于程序退出前 printf 的使用说明。
```

### chart1/.helmignore

```shell
helm package 打 tar 包时跳过哪些文件，用法同 .gitignore。
```

总结

```shell
Chart.yaml   这个文件知道你是谁
values.yaml  读values.yaml拿到默认值，再叠加-f文件合并或者set ket=values的覆盖
template/    渲染templates/下所有文件_开头文件除外，得到真的k8syaml
crds         先创建crds，再创建渲染结果
NOTES.txt    安装或者更新完才显示的内容
```

## application 与 library 类型的 chart

```shell
application是一个应用程序，可以直接被helm install部署

library类似于函数工，公共包，不能直接被部署，只提供“命名模板”（named templates）给别的 chart 用。它里面放的不是 Deployment 之类的资源模板，而是一堆可复用的模板片段，通常写在下划线开头的文件里

使用则是在每个 chart 的 Chart.yaml 里可以用 type 字段声明自己的类型：
apiVersion: v2
name: my-app
type: application   # 或者 library；不写的话默认就是 application
version: 0.1.0
```

library chart 类型

```shell
类比编程语言来理解：application chart 是“可执行程序”，library chart 是“工具函数库/公共包”。

它的特点是：

1、不能被部署。你执行 helm install 一个 library chart，Helm 会直接报错拒绝。它本身不产出任何 Kubernetes 资源。
2、只提供“命名模板”（named templates）给别的 chart 用。它里面放的不是 Deployment 之类的资源模板，而是一堆可复用的模板片段，通常写在下划线开头的文件里（如 _helpers.tpl）：

# library chart 的 _helpers.tpl 里定义的内容
{{- define "common.labels" -}}
app.kubernetes.io/name: {{ .Chart.Name }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end -}}

3、通过“依赖”的方式被引用。应用 chart 在 Chart.yaml 里声明依赖它：
# my-app 的 Chart.yaml
dependencies:
  - name: common
    version: 1.0.0
    repository: file://../common   # 或图表仓库地址
然后执行 helm dependency update 把依赖打包进 charts/ 目录。之后，应用 chart 的模板里就能直接调用库里的模板了：
# my-app 的 deployment.yaml
metadata:
  labels:
    {{- include "common.labels" . | nindent 4 }}
```

### 具体的案例

文件:

 [k8s.zip](..\..\..\zcode_data\.zcode\workspace\default\k8s.zip) 

```shell
Helm Lab：application 与 library 两种 chart 类型
三个 chart，对应三个实验。需要 Helm 3.x（VM 上先 helm version 确认）。

目录	Chart.yaml 里的 type	演示什么
01-app-chart	application（默认）	普通 chart：直接 install，渲染出真实资源
02-lib-chart	library	素材库：install 被拒、template 输出为空
03-use-lib	application	依赖 02-lib-chart，include 它的模板片段
导入虚拟机

bash
tar -xzf helm-lab.tar.gz
chmod -R u+rw helm-lab      # 保险动作：YAML 不需要执行权限，可读写即可
cd helm-lab
如果文件是经 Windows 共享文件夹拷进去的（而不是用 tar 包），建议再清一次回车符：


bash
sudo yum install -y dos2unix
find helm-lab -type f -exec dos2unix {} +
实验 A：application 直接能装

bash
helm template app1 ./01-app-chart     # 能看到渲染出的 ConfigMap
helm install app1 ./01-app-chart      # 安装成功
kubectl get configmap
helm uninstall app1
实验 B：library 不能装、也不能渲染

bash
helm show chart ./02-lib-chart        # 看到 type: library
helm template lib1 ./02-lib-chart     # 输出为空！连 extra.yaml 都不渲染
helm install lib1 ./02-lib-chart      # 报错：Error: INSTALLATION FAILED: library charts are not installable
实验 C：application 消费 library（精髓）

bash
cd 03-use-lib
helm template use1 .                  # ① 先报错：no template "lib-chart.configmap" associated（库还没拉下来）
helm dependency update                # ② 生成 charts/lib-chart-0.1.0.tgz 和 Chart.lock
ls charts/
helm template use1 .                  # ③ 现在渲染出两个 ConfigMap：
                                      #    use1-my-cm   （父 chart 自己写的）
                                      #    use1-from-lib（include 库模板生成的）
helm install use1 .
kubectl get configmap
kubectl get cm use1-from-lib -o yaml  # labels 里有 chart-type: library
helm get manifest use1                # install 后查看全量渲染结果
helm uninstall use1
验证点：两种类型的本质区别
type: application（不写就是它）：install 时渲染 templates/ 下所有文件（_ 开头除外）成 K8s 资源。
type: library：整张 chart 不可安装、不渲染任何资源（连看起来正经的 extra.yaml 也不渲染）； 它存在的意义是被别的 chart 当"素材库"，通过 include "库名.片段名" 复用。
库模板读不到父 chart 的 values —— 想传值必须在 include 时塞进 dict （见 03-use-lib/templates/from-lib.yaml 里的 $ctx 传参）。
```

总结问题

```shell
1、为什么 library chart 不能 install？
    install = 把渲染出的资源清单交给 K8s，而 library chart 渲染产物是空的
	因为library是库类型，用于共享通用模板和定义，这些 chart 通常不会被单独部署，而是作为其他chart 的依赖项被引用。
2、use1-from-lib 这个 ConfigMap 是谁、在什么时刻创建的？lib-chart 本身在集群里存在吗？
    use1-from-lib 是 helm install use1 那一刻，Helm 渲染 use-lib 的过程中生成的——具体说，是渲染父 chart 的 from-lib.yaml 时调用库片段拼出来的，然后连同 use1-my-cm 一起交给 K8s。lib-chart 在集群里根本不存在（helm list 里没有它，集群里没有任何资源属于它），它只是被借用的代码，像 helper.h 的函数住进了你的可执行文件。
    

3、库模板怎么读到父 chart 的 values？
    读不到，库模板都无法被渲染，必须显式塞入context
    通过include,对了。精确一点：include 是递值的通道，但方向是父 chart 把值递进去，不是库自己去翻。你传 . 它就能顺藤摸瓜读到 .Values，传 dict 它就只有 dict 里那几样。
4、include "xxx" 后面传的 dict 是干嘛的？
    
```

---



## 同一个 chart 依赖多次

### dependencies字段

```
在 Helm 中，一个 chart 包可能会依赖许多其他的 chart。这些依赖关系可以在两个地方指定

1、使用 Chart.yaml 中的依赖关系字段动态链接

2、也可以引入到 charts/ 目录手动进行管理。

使用 dependencies 字段管理依赖

当前 chart 所需的依赖 chart 需要在 dependencies 字段中进行定义，如下所示：

[root@k8s-master-01 01-alias]# cat Chart.yaml 

apiVersion: v2
name: alias-parent
description: 同一个 chart 依赖两次，用 alias 区分实例
type: application
version: 0.1.0

dependencies:
  - name: mini-app
    alias: frontend                        # 关键字段：给这个实例起别名
    version: "0.1.0"
    repository: "file://../mini-app"
  - name: mini-app
    alias: backend
    version: "0.1.0"
    repository: "file://../mini-app"
    
    
helm dependency update 来更新依赖项，它将根据你的依赖项文件把你
所有指定的 chart 包下载到 charts/ 目录中：  

[root@k8s-master-01 01-alias]# helm dependency update
Hang tight while we grab the latest from your chart repositories...
...Unable to get an update from the "myrepo" chart repository (http://localhost:8899):
	Get "http://localhost:8899/index.yaml": dial tcp [::1]:8899: connect: connection refused
...Successfully got an update from the "stable" chart repository
...Unable to get an update from the "prometheus-community" chart repository (https://prometheus-community.github.io/helm-charts):
	context deadline exceeded (Client.Timeout or context cancellation while reading body)
Update Complete. ⎈Happy Helming!⎈
Saving 2 charts
Deleting outdated charts


当执行 helm dependency update 命令的时候会解析 chart 的依赖项，会将他们作为 chart 包文件下
载存放到 charts/ 目录中，所以，对于上面的示例，我们可以在 charts 目录中看到如下的文件：

[root@k8s-master-01 01-alias]# ls charts/
mini-app-0.1.0.tgz




```



### 实验 1：同一个 chart 依赖多次（alias）

mini-app 是一个通用小 chart；01-alias 把它依赖了两次，别名 frontend / backend。

```bash
cd 01-alias
helm template a1 .                       # ① 先报错：依赖还没买（charts/ 是空的）
helm dependency update                   # ② 买货
ls charts/
helm template a1 .                       # ③ 三个 ConfigMap：a1-parent-note / a1-fe / a1-be
helm install a1 .
kubectl get cm
helm uninstall a1
```

重点对比 a1-fe 和 a1-be 两个 ConfigMap：

- `origin` 一模一样（都是 mini-app/templates/configmap.yaml）→ 代码只有一份，渲染了两遍
- `chart-name` 分别是 frontend / backend → helm 加载别名实例时把 .Chart.Name 换成了别名
- `message` 各自不同 → values 按"别名"分命名空间（frontend: / backend:），不再按 chart 原名



---



### 知识点 1：alias —— 同一份 chart，多个实例

**是什么**：`dependencies:` 里给依赖加 `alias:` 字段，等于对同一个 chart 声明多个**实例**。渲染时 helm 把这份 chart 复制多份，每份用别名当实例名。

**为什么需要**：真实需求是“**两个 redis，一个做缓存一个做队列，代码相同配置不同**”。没有 alias，你得复制 N 份 chart 代码，改一处要改 N 处；有了 alias，一份代码 N 个实例。

**机制（渲染时发生什么）**：

1. `helm dependency update` 只买**一份**货——charts/ 里就一个 `mini-app-0.1.0.tgz`；
2. 渲染时 helm 读父 Chart.yaml，发现两条依赖都指向 mini-app → 实例化两份；
3. 每份实例被改写两处：`.Chart.Name` 换成别名；`.Values` 只取父 values 里 `frontend:` / `backend:` 那一段；
4. 各自渲染出各自的资源，互不打架。

由此你能推出实验里的所有现象：origin 相同（代码同源）、chart-name 不同（身份不同）、message 各异（配置各归各的命名空间）。条件开关也按别名走：依赖条目里写 `condition: frontend.enabled`，配合 values 的 `frontend.enabled: false`，就能只关掉一个实例。

**类比**：`Widget a(cfg1), b(cfg2);`——类只有一份，对象两个，构造参数各配各的。alias 就是实例名，`.Chart.Name` 从“类名”变成了“实例名”。

---



## temlate目录与传入values值

```
所有在chart的template/目录下面，当helm渲染charts的时候，模板文件依赖的值Values值都可以通过两种方式提供

1. 使用默认的values.yaml文件来定义values值
2. chart包的使用者，可以自己定制包含values值的YAML文件，并不一定要交values.yaml，叫啥名都行，然后，可以在命令行通过helm install -f 来指定该文件。当用户提供自定义values值的时候，这些值将覆盖chart中values.yaml文件中的相应的值


```





## values值的作用域访问

```
[root@k8s-master-01 02-scope]# tree
.
├── charts
│   └── mid
│       ├── charts
│       │   └── leaf
│       │       ├── Chart.yaml
│       │       ├── templates
│       │       │   └── configmap.yaml
│       │       └── values.yaml
│       ├── Chart.yaml
│       ├── templates
│       │   └── configmap.yaml
│       └── values.yaml
├── Chart.yaml
├── templates
│   └── configmap.yaml
└── values.yaml

7 directories, 9 files

value.yaml和Chart.yaml文件查看
1、top

[root@k8s-master-01 02-scope]# cat Chart.yaml 
apiVersion: v2
name: scope-top
description: 多级 chart values 作用域演示（子 chart 直接放 charts/ 目录，无需 dependencies 声明）
type: application
version: 0.1.0

[root@k8s-master-01 02-scope]# cat values.yaml 
greeting: "i am scope-top (parent)"

# global 键会被 helm 自动穿透到所有层级的 .Values.global
global:
  env: "prod-cluster"

# 想传给子 chart，就必须以子 chart 的名字开头
mid:
  greeting: "top -> mid (override)"
  # 还可以隔层传话：mid 名下再嵌 leaf 段，会继续下钻给 leaf
  leaf:
    greeting: "top -> mid -> leaf (grandpa speaks to grandson)"


2、mid

[root@k8s-master-01 02-scope]# cd charts/mid/
[root@k8s-master-01 mid]# cat Chart.yaml 
apiVersion: v2
name: midc
description: 中间层
type: application
version: 0.1.0
[root@k8s-master-01 mid]# cat values.yaml 
greeting: "i am mid (my own default)"

# 我是 leaf 的父级，leaf 的默认值写在"我"的 values 里
leaf:
  greeting: "i am leaf (mid's default)"


3、leaf

[root@k8s-master-01 mid]# cd charts/leaf/
[root@k8s-master-01 leaf]# cat values.yaml 
greeting: "i am leaf (my own default)"
[root@k8s-master-01 leaf]# cat Chart.yaml 
apiVersion: v2
name: leaf
description: 最内层
type: application
version: 0.1.0


template模板文件引入上级chart包的变量

0、top

[root@k8s-master-01 02-scope]# cat templates/configmap.yaml 
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-top
data:
  who: "scope-top"
  greeting: {{ .Values.greeting | quote }}
  globalEnv: {{ .Values.global.env | quote }}

1、mid

[root@k8s-master-01 02-scope]# cat charts/mid/templates/configmap.yaml 
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-mid
data:
  who: "mid"
  greeting: {{ .Values.greeting | quote }}
  globalEnv: {{ .Values.global.env | quote }}
  leafInMyScope: {{ .Values.leaf.greeting | quote }}
  
 2、leaf
 
 [root@k8s-master-01 02-scope]# cat charts/mid/charts/leaf/templates/configmap.yaml 
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-leaf
data:
  who: "leaf"
  greeting: {{ .Values.greeting | quote }}
  globalEnv: {{ .Values.global.env | quote }}
```



```3
wordpreee Chart   父chart，顶级chart
   自己的values.yaml中定义的一切都可以访问，按照正常的层级访问即可，
   	1、可以通过 .Values.title 访问title值
   	2、可以通过 .Values.mysql.password、.Value.mysql.max_connections访问MySQL用的values
    3、可以通过 .Values.apache.port 访问 Apache 的values。
MySQL chart 
	只能访问父Chart给自己定义的内容，以及父级中global下定义的内容
	.Values.global.app访问
Apache chart 
 	同上
    
```





## values.schema.json 校验

```
values.schema.json 用来验证 values.yaml 文件中定义的 value 值，以及命令 --set 传入的 value 值的结构和数据类型

values.schema.json将应用于最终的 `.Values` 对象、而不仅仅是应用于 `value.yaml` 文件，也就是说你在命令行helm install a1 --set key=value 传入的也需要遵守schema 

真实案例说明：
	为什么需要：values.yaml 只是默认值，用户可以 --set 任何鬼东西。没有 schema，错误会拖到渲染中途甚至运行时才爆，报错难懂；有了 schema，错误拦在门口、指名道姓（"replicas 不能超过 10"）。对内是防手滑，对外是给 chart 用户的配置契约，CI 里也能提前失败。
	
语法五件套：
	type（类型）、
	enum（枚举三选一）、
	minimum/maximum（数值范围）、
	minLength/pattern（字符串约束）、
	required + properties（必填与嵌套结构）。

类比：函数签名 / 编译期类型检查。values.yaml 的值是实参，schema 是形参的类型声明——类型不对直接“编译失败”，而不是运行到一半 core dump。等于给配置结构体写了一份类型声明头文件。

怎么用：对外发布的 chart 必带；内部 chart 至少给易错字段加（端口范围、enum、必填项）。
```

案例

```yaml

[root@k8s-master-01 03-schema]# cat values.schema.json 
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "schema-app values",
  "type": "object",
  "required": ["name", "replicas", "image"],
  "properties": {
    "name": {
      "type": "string",
      "minLength": 3,
      "description": "应用名，至少 3 个字符"
    },
    # 副本数量
    "replicas": {
      "type": "integer",
      "minimum": 1,
      "maximum": 10
    },
    # 镜像:变量传入锁死格式和类型
    "image": {
      "type": "object",
      "required": ["repository"],
      "properties": {
        "repository": { "type": "string" },
        "tag": { "type": "string" }
      }
    },
    # 限制输入内容
    "service": {
      "type": "object",
      "properties": {
        "type": {
          "type": "string",
          "enum": ["ClusterIP", "NodePort", "LoadBalancer"]
        }
      }
    },
    # 限制输入范围
    "port": {
      "type": "integer",
      "minimum": 1,
      "maximum": 65535
    }
  }
}

```

验证

```bash
#副本数量超出

[root@k8s-master-01 03-schema]# helm template v1 . --set replicas=20
Error: values don't meet the specifications of the schema(s) in the following chart(s):
schema-app:
- replicas: Must be less than or equal to 10


# 字符数量不达标
helm template v1 . --set name=ab                      # 报错：最少 3 个字符

# 超出给定范围
helm template v1 . --set service.type=HostPort        # 报错：enum 三选一

# 类型错误
helm template v1 . --set port=abc                     # 报错：类型必须是 integer

```

总结

```
校验发生在**渲染之前**——配置不合法根本走不到模板那一步，报错直接指到字段。
schema 只检查你写进 schema 的字段，没写的字段不管。
进阶：想禁止用户乱传字段，在 schema 根上加 "additionalProperties": false（自己试）。
```



## CRDS、LICENSE、Notes 了解

### CRDS

crds/：（放 CRD，注册新资源类型）,不模板化（写什么发什么，不吃 values）、install 时先于一切资源、uninstall 不删
—— 类比 C++：先向编译器注册新类型（先声明后使用），声明本身不参与"编译"



### LICENSE

LICENSE：不参与任何渲染，只是随包携带，发布到 Artifact Hub 等仓库时会展示



### Notes

templates/NOTES.txt：也是模板，但渲染结果不发 K8s，只在 install/upgrade 后打印
—— 类比 main() 结束前的 printf







# chart 存储

## 1、使用 helm 管理 chart 包

```
helm create mychart

ls

helm package mychart

ls
运行helm package命令时，helm会将指定的helmchart 打包成一个.tgz格式的helm包命令，这是将chart准备好以便分发和部署的重要步骤。具体来说，helm package mychart 主要做了一下几种事情
1、验证chart 结构
	检查 mychart 目录是否包含必需的文件和目录，诸如 Chart.yaml、values.yaml、templates 目录
等。
	确保 Chart.yaml 文件中定义的 chart 名称、版本等信息是有效的。

2、处理依赖关系
	如果 mychart 中的 Chart.yaml 定义了依赖关系（例如包含 dependencies 字段），Helm 会检查这
些依赖是否已经满足。如果依赖的 chart 包不在本地，将操作失败并提示错误，用户需要先运行 helm
dependency update 来获取这些依赖。

3、创建包文件
将 mychart 目录及其所有内容（包括 values.yaml、templates 等）打包成一个 .tgz 文件。这个文
件名由 Chart.yaml 中定义的 chart 名称和版本决定，格式为 mychart-<版本号>.tgz。
```



## 2、构建一个自己的chart私有仓库

可以提供Yaml文件和tar文件并可以响应GET请求的任何HTTP服务器都可以作为chart仓库服务器

来部署一个私有的chart仓库

chart仓库服务端准备

1、启动一个http服务。监听8888端口



2、仓库的主要特征是存在一个名为index.yaml的特殊文件，该文件具有仓库中提供的所有软件包的列表以及允许检索和验证这些软件包的元数据

```
1、创建chart包
	cd /usr/share/nginx/html
	helm create mychart
# 2、打包chart
	helm package mychart
# 3、当前目录下生成一个 index.yaml 文件，每新增一个chart都要重新执行下条命令来生成
	index.yaml
	helm repo index . --url http://192.168.71.12:8888
	helm repo index . --url http://192.168.71.113:
	
客户端
1、添加私有仓库
	helm repo add myprivaterepo http://192.168.71.12:888
2、开始验证
	# 1、更新本地仓库缓存，你的仓库内容变动过，那你就需要更新一下才能看到
		helm repo update
	# 你也可以curl http://192.168.71.12:8888/index.yaml验证一下目标仓库的该文件是否真的更新了
	# 2、检索仓库
		helm search repo myprivaterepo
	# 3、安装
		helm install myrelease myprivaterepo/mychart
```





## 3、构建 harbor 管理 chart 包版本

### 先装nfs

```
① NFS 服务器（一台 VM 上跑的普通 Linux 服务）  ← 你缺的是这个！
② NFS 客户端工具（每个 k8s 节点的 nfs-utils）  ← 刚修好
③ provisioner（helm 装的那个 chart）          ← 它只是中间人：盯着 PVC，替你到 ① 上建子目录
```

helm 装的 nfs-subdir-external-provisioner **不是** NFS 服务器，它假设 10.0.0.201 上已经有一个跑起来的 NFS 服务。现在那台机器上要么没装、要么装了没启动、要么装在了别的机器上。

##### 在 10.0.0.201 那台机器上跑（先 `ip a` 确认它真的是 .201）

```
yum install -y nfs-utils
mkdir -p /data/nfs
echo "/data/nfs *(rw,no_root_squash,sync)" > /etc/exports
systemctl enable --now nfs-server
```

##### **“怎么才算装对”——三连验收，一条不过就别往下走：**

```
systemctl status nfs-server     # ① 必须是 active (running)
exportfs -v                     # ② 必须列出 /data/nfs 一行
systemctl stop firewalld        # ③ lab 环境直接停（refused 也可能是它 REJECT 的）
```

#### 回到 node-02 上验证（客户端视角）

![img](F:/Soft/Typora/Typora/material-icons/console.svg)bash

```
showmount -e 10.0.0.201                       # 列出 /data/nfs → 服务器这边通了
mount -t nfs 10.0.0.201:/data/nfs /mnt \
  && ls /mnt && umount /mnt                   # kubelet 失败的那个挂载，手动成功 = 根治
```

这两条过了，Pod 一两分钟内自愈转 Running（急的话 `kubectl delete pod` 让它重来）。然后照旧可能撞镜像拉取——解药我前面给过（`helm upgrade --reuse-values` 换华为云源），报 `ImagePullBackOff` 再用。

```
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/


helm upgrade --install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner --set nfs.server=10.0.0.201 --set nfs.path=/data/nfs --set storageClass.defaultClass=true -n kube-system
```

### 安装harbor仓库

```
helm repo add harbor https://helm.goharbor.io

helm repo list

helm pull harbor/harbor

tar zxvf harbor-1.14.2.tgz
```

### values.yaml文件修改

```
cd /harbor
ls
vim values.yaml
```

**1、更改service代理类型**

````
1 expose:
2   # Set how to expose the service. Set the type as "ingress", "clusterIP", "nodePort", "loadBalancer" or "route"
3   # and fill the information in the corresponding section
4   type: nodePort
````

**2、关闭tls认证**

```
  5   tls:
  11     enabled: false
```

**3、使用http协议**

```
 126 #externalURL: https://core.harbor.domain
 127 externalURL: http://10.0.0.201:30002
```

**4、持久化存储配置部分 主要修改nfs存储卷**

```
 136 persistence:
 137   enabled: true
 142   resourcePolicy: "keep"
 143   persistentVolumeClaim:
 144     registry:
 151       storageClass: "nfs-client"
 152       subPath: ""
 153       accessMode: ReadWriteOnce
 154       size: 5Gi
 155       annotations: {}
 156     jobservice:
 157       jobLog:
 158         existingClaim: ""
 159         storageClass: "nfs-client"
 160         subPath: ""
 161         accessMode: ReadWriteOnce
 162         size: 1Gi
 163         annotations: {}
 166     database:
 167       existingClaim: ""
 168       storageClass: "nfs-client"
 169       subPath: ""
 170       accessMode: ReadWriteOnce
 171       size: 1Gi
 172       annotations: {}
 175     redis:
 176       existingClaim: ""
 177       storageClass: "nfs-client"
 178       subPath: ""
 179       accessMode: ReadWriteOnce
 180       size: 1Gi
 181       annotations: {}
 182     trivy:
 183       existingClaim: ""
 184       storageClass: "nfs-client"
 185       subPath: ""
 186       accessMode: ReadWriteOnce
 187       size: 5Gi
 188       annotations: {}
```

**5、密码更改**

```
 290 harborAdminPassword: "zhuaoxue666"
```

**6、metrics监控 可以使用prometheus监控harbor指标**

```

 410 metrics:
 411   enabled: false
 412   core:
 413     path: /metrics
 414     port: 8001
 415   registry:
 416     path: /metrics
 417     port: 8001
 418   jobservice:
 419     path: /metrics
 420     port: 8001
 421   exporter:
 422     path: /metrics
 423     port: 8001
```

**安装**

```
kubectl create namespace harbor 
kubectl get ns 
helm install harbor . -n harbor
kubectl get posd -n harbor
kubectl get pods -n harbor

上传镜像到阿里云容器仓库

kubectl -n harbor get svc

kubectl -n harbor get pods

登录  https://10.0.0.201:30002
admin
zhuaoxue666

```

## 将harbor当chart库使用

```
1、登录harbor仓库
helm registry login -u admin -o zhuaoxue666 --insecure 10.0.0.201:30002

2、本地打包
helm create zzz
helm package zzz

3、push上传，注意报的格式重就包含了push之后的版本信息
helm push harbor-1.19.4.tgz --plain-http oci://10.0.0.201:30002/chart-repo/

4、pull下载
 1052  helm pull --plain-http oci://10.0.0.201:30002/chart-repo/harbor --version 1.19.4

5、重复步骤2，然后在package之后修改chart.yaml的version版本，然后打包后push
helm package zzz

helm push zzz-0.1.1.tgz --plain-http oci://10.0.0.201:30002/chart-repo

6、安装
helm install zzz-releasel --plain-http oci://10.0.0.201:30002/chart-repo/zzz --version 0.1.0
kubectl get pods zzz-release  -w
```

## 当镜像仓库使用

```
nerdctl loging -u admin -p zhuaoxue666 --insecure-registry http:/10.0.0.2:30002

nerdctl -n k8s.io tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9
10.0.0.201:30002/library/pause:3.9

nerdctl -n k8s.io push --insecure-registry 10.0.0.201:30002/library/pause:3.9
```







# chart 模板开发语法



## 内置对象用法详解





函数与管道



流程控制

if 多分支及 ident 控制



with 声明作用域



range 语法及作用域



变量的定义与$符号应用

全局命令文件及 template 与 include



引入外部普通文件及处理



NOTES.txt 文件提示信息定制