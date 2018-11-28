# NFS 动态存储卷

## 添加 choerodon chart 仓库

```makefile
helm repo add c7n https://openchart.choerodon.com.cn/choerodon/c7n/
helm repo update
```

## 没有 NFS 服务

-   在集群每一个节点安装 `nfs-utils`

```makefile
sudo yum install -y nfs-utils
```

-   在任意一个 master 节点执行下面 helm 命令，安装 `nfs-provisioner`

```makefile
helm install c7n/nfs-provisioner \
    --set rbac.create=true \
    --set service.enabled=true \
    --set persistence.enabled=true \
    --set persistence.nodeName=masternode \
    --set persistence.hostPath=/opt/prod \
    --version 0.1.0 \
    --name nfs-provisioner \
    --namespace c7n-system
```
