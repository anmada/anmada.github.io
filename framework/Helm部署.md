# Helm 部署

## 创建 ServiceAccount

-   在任意一个 master 节点执行以下命令

```makefile
kubectl create serviceaccount --namespace kube-system helm-tiller
kubectl create clusterrolebinding helm-tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:helm-tiller
```

## 部署客户端

### 在任意一个 master 节点执行以下命令

1.  根据系统下载所需版本

```makefile
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.8.2-linux-amd64.tar.gz
```

2. 解压压缩包（以 linux-amd64 为例）

```makefile
tar -zxvf helm-v2.8.2-linux-amd64.tar.gz
```

3. 将文件移动到 PATH 目录中（以 linux-amd64 为例）

```makefile
mv linux-amd64/helm /usr/bin/helm
```

4. 初始化 Helm

```makefile
helm init \
    --tiller-image=registry.cn-shanghai.aliyuncs.com/choerodon/tiller:v2.8.2 \
    --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts \
    --service-account=helm-tiller
```

## 验证部署

-   执行命令，出现以下信息即部署成功。

```makefile
$ helm version
Client: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844",GitTreeState:"clean"}
```
