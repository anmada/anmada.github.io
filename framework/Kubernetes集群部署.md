# Kubernetes 集群部署

## 阿里云安装示例

### 环境准备

-   在要执行 ansible 脚本的机器上部署 ansible 运行需要的环境：

```bash
sudo yum install -y epel-release && \
sudo yum install -y \
    ansible \
    git \
    httpd-tools \
    pyOpenSSL \
    python-cryptography \
    python-lxml \
    python-netaddr \
    python-passlib \
    python-pip

# 查看ansible版本（version>=2.4.0.0）
ansible --version
```

-   克隆项目：

```bash
git clone https://github.com/choerodon/kubeadm-ansible.git
```

### 修改 hosts 文件

?> 在阿里云的 ECS 的控制面板上修改 ECS 实例的 hostname，名称最好只包含小写字母、数字和中划线。并保持与 inventory/hosts 中的名称与 ECS 控制台上的名称保持一致，重启生效。

!> Etcd 节点和 Master 节点必须一致。

-   编辑项目下的`kubeadm-ansible/inventory/hosts`文件，修改各机器的访问地址、用户名、密码，并维护好各节点与角色的关系，前面的名称为机器的 hostname。该用户必须是具有 root 权限的用户。但并非要求一定是 root 用户，其他具有 root 权限的用户也可以。比如，想要部署单节点集群，只需要这样配置(参考)：

```
[all]
masternode ansible_host=172.16.101.89 ip=172.16.101.89 ansible_user=root ansible_ssh_pass=Qwe123456_!_! ansible_become=true

[kube-master]
masternode

[etcd]
masternode

[kube-node]
masternode
```

?> ansible_host 指节点内网 IP，IP 指 Kubernetes 目标绑定网卡 IP

### 修改 kubeadm-ansible/inventory/vars 变量文件

?> 本文档部署的网络类型为 flannel 类型

-   在使用 VPC 网络的 ECS 上部署 k8s 时，flannel 网络的 Backend 类型需要是 `ali-vpc`。在本脚本中默认使用的是 vxlan 类型，虽然在 vpc 环境下网络能通，但是不稳定波动较大。所以推荐使用 `ali-vpc` 的类型。
-   因此，首先需要设置默认的 flannel 网络不部署，通过在 `kubeadm-ansible/inventory/vars` 文件中添加变量：

```makefile
flannel_enable: false
```

-   网段选择，如果 ECS 服务器用的是专有网络，pod 和 service 的网段不能与 vpc 网段重叠，若有重叠请配置 kube_pods_subnet 和 kube_service_addresses 变量设置 pod 和 service 的网段，示例参考：

```makefile
# 如果vpc网段为`172.*`
kube_pods_subnet: 192.168.0.0/20
kube_service_addresses: 192.168.255.0/20

# 如果vpc网段为`10.*`
kube_pods_subnet: 172.16.0.0/16
kube_service_addresses: 172.19.0.0/20

# 如果vpc网段为`192.168.*`
kube_pods_subnet: 172.16.0.0/16
kube_service_addresses: 172.19.0.0/20
```

```makefile
# 默认参数定义在roles/base/variables/default/main.yml,如果要修改,可在本文件中覆>盖
flannel_enable: false
# 暂时只支持v1.7.0、v1.8.1、v1.8.5
kube_version: "v1.8.5"

# pod的ip范围
kube_pods_subnet: 192.168.0.0/20

# service的ip范围
kube_service_addresses: 192.168.255.0/20

# kube-lego组件邮箱
kube_lego_email: example@choerodon.io
```

## 部署

-   下面开始部署集群：

```makefile
export ANSIBLE_HOST_KEY_CHECKING=False
ansible-playbook -i inventory/hosts -e @inventory/vars cluster.yml -K
```

-   查看等待 pod 的状态为 runnning：

```makefile
kubectl get po -n kube-system
```

-   如果部署失败，想要重置集群(所有数据),执行：

```makefile
ansible-playbook -i inventory/hosts reset.yml
```

-   [获取阿里云 ACCESS_KEY](https://help.aliyun.com/knowledge_detail/38738.html)，该 ACCESS_KEY 需要拥有以下权限：

    -   只读访问云服务器(ECS)的权限
    -   管理专有网络(VPC)的权限

-   手动部署 flannel 网络插件，在任意一个 Master 节点创建配置文件 `kube-flannel-aliyun.yml`：

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
    name: flannel
rules:
    - apiGroups:
          - ''
      resources:
          - pods
      verbs:
          - get
    - apiGroups:
          - ''
      resources:
          - nodes
      verbs:
          - list
          - watch
    - apiGroups:
          - ''
      resources:
          - nodes/status
      verbs:
          - patch
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
    name: flannel
roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: flannel
subjects:
    - kind: ServiceAccount
      name: flannel
      namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
    name: flannel
    namespace: kube-system
---
kind: ConfigMap
apiVersion: v1
metadata:
    name: kube-flannel-cfg
    namespace: kube-system
    labels:
        tier: node
        app: flannel
data:
    cni-conf.json: |
        {
          "name": "cbr0",
          "type": "flannel",
          "delegate": {
            "isDefaultGateway": true
          }
        }
    net-conf.json: |
        {
          "Network": "192.168.0.0/20",
          "Backend": {
            "Type": "ali-vpc"
          }
        }
---
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
    name: kube-flannel-ds
    namespace: kube-system
    labels:
        tier: node
        app: flannel
spec:
    template:
        metadata:
            labels:
                tier: node
                app: flannel
        spec:
            hostNetwork: true
            nodeSelector:
                beta.kubernetes.io/arch: amd64
            tolerations:
                - key: node-role.kubernetes.io/master
                  operator: Exists
                  effect: NoSchedule
            serviceAccountName: flannel
            initContainers:
                - name: install-cni
                  image: registry.cn-hangzhou.aliyuncs.com/google-containers/flannel:v0.9.0
                  command:
                      - cp
                  args:
                      - -f
                      - /etc/kube-flannel/cni-conf.json
                      - /etc/cni/net.d/10-flannel.conf
                  volumeMounts:
                      - name: cni
                        mountPath: /etc/cni/net.d
                      - name: flannel-cfg
                        mountPath: /etc/kube-flannel/
            containers:
                - name: kube-flannel
                  image: registry.cn-hangzhou.aliyuncs.com/google-containers/flannel:v0.9.0
                  command: ['/opt/bin/flanneld', '--ip-masq', '--kube-subnet-mgr']
                  securityContext:
                      privileged: true
                  env:
                      - name: POD_NAME
                        valueFrom:
                            fieldRef:
                                fieldPath: metadata.name
                      - name: POD_NAMESPACE
                        valueFrom:
                            fieldRef:
                                fieldPath: metadata.namespace
                      - name: ACCESS_KEY_ID
                        value: LTAImurJAkTpkHJo
                      - name: ACCESS_KEY_SECRET
                        value: R69PbVyBINTKLohzgu1077lA6LtSaN
                  volumeMounts:
                      - name: run
                        mountPath: /run
                      - name: flannel-cfg
                        mountPath: /etc/kube-flannel/
            volumes:
                - name: run
                  hostPath:
                      path: /run
                - name: cni
                  hostPath:
                      path: /etc/cni/net.d
                - name: flannel-cfg
                  configMap:
                      name: kube-flannel-cfg
```

-   然后使用 kubectl 命令部署，部署成功后在 vpc 的路由表中会添加多条路由条目，下一跳分别为每个节点的 pod ip 段：
```makefile
kubectl apply -f kube-flannel-aliyun.yml
```