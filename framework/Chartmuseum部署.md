## 添加choerodon chart仓库并同步
```makefile
helm repo add c7n https://openchart.choerodon.com.cn/choerodon/c7n/
helm repo update
```

## 部署Chartmuseum
### 创建PVC
```makefile
helm install c7n/persistentvolumeclaim \
    --set requests.storage=256Mi \
    --set accessModes={ReadWriteMany} \
    --set storageClassName=nfs-provisioner \
    --version 0.1.0 \
    --name chartmuseum-pvc \
    --namespace c7n-system
```

### 进行部署
```makefile
helm install c7n/chartmuseum \
    --set service.enabled=true \
    --set persistence.enabled=true \
    --set persistence.existingClaim=chartmuseum-pvc \
    --set ingress.enabled=true \
    --set ingress.hosts=chart.amd.tonkor.com \
    --set env.open.DISABLE_API=false \
    --set env.open.DEPTH=2 \
    --version 1.6.1 \
    --name chartmuseum \
    --namespace c7n-system


helm install c7n/minio \
    --set service.enabled=true \
    --set persistence.enabled=true \
    --set persistence.existingClaim=minio-pvc \
    --set env.open.MINIO_ACCESS_KEY=admin \
    --set env.open.MINIO_SECRET_KEY=password \
    --set ingress.enabled=true \
    --set ingress.hosts=minio.amd.tonkor.com \
    --version 0.1.0 \
    --name minio \
    --namespace c7n-system

helm install c7n/zookeeper \
    --set replicaCount=1 \
    --set service.enabled=true \
    --set persistence.enabled=true \
    --set persistence.storageClass="nfs-provisioner" \
    --version 0.1.0 \
    --name zookeeper \
    --namespace c7n-system

helm install c7n/kafka \
    --set replicaCount=1 \
    --set service.enabled=true \
    --set persistence.enabled=true \
    --set persistence.storageClass="nfs-provisioner" \
    --set zookeeperConnect="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --version 0.1.0 \
    --name kafka \
    --namespace c7n-system

helm install c7n/harbor \
    --set externalURL=https://registry.amd.tonkor.com \
    --set ingress.hosts.core=registry.amd.tonkor.com \
    --set database.internal.volumes.data.storageClass="nfs-provisioner" \
    --set registry.volumes.data.storageClass="nfs-provisioner" \
    --set redis.master.persistence.storageClass="nfs-provisioner" \
    --set harborAdminPassword=Harbor12345 \
    --version 0.3.2 \
    --name harbor \
    --namespace c7n-system


helm install c7n/gitlab \
    --set persistence.enabled=true \
    --set persistence.existingClaim=gitlab-pvc \
    --set env.config.GITLAB_EXTERNAL_URL=http://gitlab.amd.tonkor.com \
    --set env.config.GITLAB_TIMEZONE=Asia/Shanghai \
    --set env.config.CHOERODON_OMNIAUTH_ENABLED=false \
    --set env.config.GITLAB_DEFAULT_CAN_CREATE_GROUP=true \
    --set env.config.MYSQL_HOST=gitlab-mysql.c7n-system.svc \
    --set env.config.MYSQL_USERNAME=root \
    --set env.config.MYSQL_PASSWORD=password \
    --set env.config.MYSQL_DATABASE=gitlabhq_production \
    --set env.config.REDIS_HOST=gitlab-redis.c7n-system.svc \
    --set env.config.SMTP_ENABLE=true \
    --set env.config.SMTP_ADDRESS=smtp.mxhichina.com \
    --set env.config.SMTP_PORT=465 \
    --set env.config.SMTP_USER_NAME=git.sys@example.com \
    --set env.config.SMTP_PASSWORD=password \
    --set env.config.SMTP_DOMAIN=smtp.mxhichina.com \
    --set env.config.SMTP_AUTHENTICATION=login \
    --set env.config.GITLAB_EMAIL_FROM=git.sys@example.com \
    --set env.config.SMTP_ENABLE_STARTTLS_AUTO=true \
    --set env.config.SMTP_TLS=true \
    --set env.config.PROMETHEUS_ENABLE=false \
    --set env.config.NODE_EXPORTER_ENABLE=false \
    --set service.enabled=true \
    --set ingress.enabled=true \
    --version 0.2.0 \
    --name gitlab \
    --namespace c7n-system

helm install c7n/mysql-client \
    --set env.MYSQL_HOST=rm-bp1vs7555641tkb85.mysql.rds.aliyuncs.com \
    --set env.MYSQL_PORT=3306 \
    --set env.MYSQL_USER=root \
    --set env.MYSQL_PASS=BuYomZzln7HdvAEf1lOlcqDoftEsohg= \
    --set env.SQL_SCRIPT="\
          CREATE USER IF NOT EXISTS 'choerodon'@'%' IDENTIFIED BY 'password';\
          CREATE DATABASE IF NOT EXISTS iam_service DEFAULT CHARACTER SET utf8;\
          CREATE DATABASE IF NOT EXISTS manager_service DEFAULT CHARACTER SET utf8;\
          CREATE DATABASE IF NOT EXISTS asgard_service DEFAULT CHARACTER SET utf8;\
          CREATE DATABASE IF NOT EXISTS notify_service DEFAULT CHARACTER SET utf8;\
          GRANT ALL PRIVILEGES ON iam_service.* TO choerodon@'%';\
          GRANT ALL PRIVILEGES ON manager_service.* TO choerodon@'%';\
          GRANT ALL PRIVILEGES ON asgard_service.* TO choerodon@'%';\
          GRANT ALL PRIVILEGES ON notify_service.* TO choerodon@'%';\
          FLUSH PRIVILEGES;" \
    --version 0.1.0 \
    --name create-c7nfw-db \
    --namespace c7n-system

helm install c7n/go-register-server \
    --set service.enable=true \
    --set service.name=register-server \
    --set env.open.REGISTER_SERVICE_NAMESPACE="c7n-system" \
    --set env.open.KAFKA_ADDRESSES="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --name register-server \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/config-server \
    --set service.enable=true \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name config-server \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/manager-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preInitDB.datasource.username=choerodon \
    --set preJob.preInitDB.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.CHOERODON_GATEWAY_DOMAIN="api.amd.tonkor.com" \
    --set env.open.CHOERODON_SWAGGER_OAUTH_URL="http://api.amd.tonkor.com/oauth/oauth/authorize" \
    --set env.open.SPRING_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name manager-service2 \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/asgard-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/asgard_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preInitDB.datasource.username=choerodon \
    --set preJob.preInitDB.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/asgard_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.CHOERODON_ASGARD_ISLOCAL=false \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name asgard-service2 \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/notify-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/notify_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preInitDB.datasource.username=choerodon \
    --set preJob.preInitDB.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/notify_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
    --set env.open.SPRING_REDIS_DATABASE=1 \
    --set service.enable=true \
    --set service.name=notify-service \
    --set ingress.enable=true \
    --set ingress.host=notify.amd.tonkor.com \
    --name notify-service \
    --version 0.10.0 \
    --namespace c7n-system


helm install c7n/iam-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/iam_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preInitDB.datasource.username=choerodon \
    --set preJob.preInitDB.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/iam_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name iam-service \
    --version 0.10.1 \
    --namespace c7n-system

helm install c7n/api-gateway \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set service.enable=true \
    --set ingress.enable=true \
    --set ingress.host=api.amd.tonkor.com \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name api-gateway1 \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/gateway-helper \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/iam_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name gateway-helper \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/oauth-server \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/iam_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
    --set env.open.SPRING_REDIS_DATABASE=2 \
    --set env.open.CHOERODON_DEFAULT_REDIRECT_URL="http://c7n.amd.tonkor.com" \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name oauth-server \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/file-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set env.open.MINIO_ENDPOINT="http://minio.amd.tonkor.com" \
    --set env.open.MINIO_ACCESSKEY=admin \
    --set env.open.MINIO_SECRETKEY=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --name file-service \
    --version 0.10.0 \
    --namespace c7n-system

helm install c7n/mysql-client \
    --set env.MYSQL_HOST=c7n-mysql.c7n-system.svc \
    --set env.MYSQL_PORT=3306 \
    --set env.MYSQL_USER=root \
    --set env.MYSQL_PASS=password \
    --set env.SQL_SCRIPT="\
          CREATE USER IF NOT EXISTS 'choerodon'@'%' IDENTIFIED BY 'password';\
          CREATE DATABASE IF NOT EXISTS devops_service DEFAULT CHARACTER SET utf8;\
          CREATE DATABASE IF NOT EXISTS gitlab_service DEFAULT CHARACTER SET utf8;\
          GRANT ALL PRIVILEGES ON devops_service.* TO choerodon@'%';\
          GRANT ALL PRIVILEGES ON gitlab_service.* TO choerodon@'%';\
          FLUSH PRIVILEGES;" \
    --version 0.1.0 \
    --name create-c7ncd-db \
    --namespace c7n-system

helm install c7n/devops-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preConfig.mysql.port=3306 \
    --set preJob.preConfig.mysql.database=manager_service \
    --set preJob.preConfig.mysql.username=choerodon \
    --set preJob.preConfig.mysql.password=password \
    --set preJob.preInitDB.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preInitDB.mysql.port=3306 \
    --set preJob.preInitDB.mysql.database=devops_service \
    --set preJob.preInitDB.mysql.username=choerodon \
    --set preJob.preInitDB.mysql.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/devops_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
    --set env.open.SPRING_REDIS_DATABASE=3 \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SERVICES_HARBOR_BASEURL="https://registry.amd.tonkor.com" \
    --set env.open.SERVICES_HARBOR_USERNAME=admin \
    --set env.open.SERVICES_HARBOR_PASSWORD="Harbor12345" \
    --set env.open.SERVICES_HELM_URL="http://chart.amd.tonkor.com" \
    --set env.open.SERVICES_GITLAB_URL="http://gitlab.amd.tonkor.com" \
    --set env.open.SERVICES_GITLAB_SSHURL="gitlab.amd.tonkor.com" \
    --set env.open.SERVICES_GITLAB_PASSWORD=letmein_1114 \
    --set env.open.SERVICES_GITLAB_PROJECTLIMIT=100 \
    --set env.open.SERVICES_GATEWAY_URL=http://api.amd.tonkor.com \
    --set env.open.SECURITY_IGNORED="/ci\,/webhook\,/v2/api-docs\,/agent/**\,/ws/**\,/webhook/**" \
    --set env.open.AGENT_VERSION="0.10.0" \
    --set env.open.AGENT_REPOURL="https://openchart.choerodon.com.cn/choerodon/c7n/" \
    --set env.open.AGENT_SERVICEURL="ws://devops.amd.tonkor.com/agent/" \
    --set env.open.TEMPLATE_VERSION_MICROSERVICE="0.10.0" \
    --set env.open.TEMPLATE_VERSION_MICROSERVICEFRONT="0.10.0" \
    --set env.open.TEMPLATE_VERSION_JAVALIB="0.10.0" \
    --set ingress.enable=true \
    --set ingress.host=devops.amd.tonkor.com \
    --set service.enable=true \
    --set persistence.enabled=true \
    --set persistence.existingClaim="chartmuseum-pvc" \
    --name devops-service \
    --version 0.10.4 \
    --namespace c7n-system


helm install c7n/gitlab-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preConfig.mysql.port=3306 \
    --set preJob.preConfig.mysql.database=manager_service \
    --set preJob.preConfig.mysql.username=choerodon \
    --set preJob.preConfig.mysql.password=password \
    --set preJob.preInitDB.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preInitDB.mysql.port=3306 \
    --set preJob.preInitDB.mysql.database=gitlab_service \
    --set preJob.preInitDB.mysql.username=choerodon \
    --set preJob.preInitDB.mysql.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/gitlab_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.GITLAB_URL="http://gitlab.amd.tonkor.com" \
    --set env.open.GITLAB_PRIVATETOKEN="BQokH2bzb7F_sizygsJU" \
    --name gitlab-service \
    --version 0.10.0 \
    --namespace c7n-system

helm upgrade gitlab c7n/gitlab \
    -f <(helm get values gitlab) \
    --set env.config.CHOERODON_OMNIAUTH_ENABLED=true \
    --set env.config.OMNIAUTH_AUTO_SIGN_IN_WITH_PROVIDER=oauth2_generic \
    --set env.config.OMNIAUTH_BLOCK_AUTO_CREATED_USERS=false \
    --set env.config.CHOERODON_API_URL=http://api.amd.tonkor.com \
    --set env.config.CHOERODON_CLIENT_ID=gitlab \
    --version 0.2.0 \
    --namespace c7n-system

helm install c7n/mysql-client \
        --set env.MYSQL_HOST=c7n-mysql.c7n-system.svc \
        --set env.MYSQL_PORT=3306 \
        --set env.MYSQL_USER=root \
        --set env.MYSQL_PASS=password \
        --set env.SQL_SCRIPT="\
            INSERT INTO iam_service.oauth_client ( \
            name\,organization_id\,resource_ids\,secret\,scope\,\
            authorized_grant_types\,web_server_redirect_uri\,\
            access_token_validity\,refresh_token_validity\,\
            additional_information\,auto_approve\,object_version_number\,\
            created_by\,creation_date\,last_updated_by\,last_update_date)\
            VALUES('gitlab'\,1\,'default'\,'secret'\,'default'\,\
            'password\,implicit\,client_credentials\,authorization_code\,refresh_token'\,\
            'http://gitlab.amd.tonkor.com'\,3600\,3600\,'{}'\,'default'\,1\,0\,NOW()\,0\,NOW());" \
        --version 0.1.0 \
        --name gitlab-client \
        --namespace c7n-system

helm install c7n/mysql-client \
    --set env.MYSQL_HOST=rm-bp1vs7555641tkb85.mysql.rds.aliyuncs.com \
    --set env.MYSQL_PORT=3306 \
    --set env.MYSQL_USER=root \
    --set env.MYSQL_PASS=BuYomZzln7HdvAEf1lOlcqDoftEsohg= \
    --set env.SQL_SCRIPT="\
        INSERT INTO gitlabhq_production.identities(extern_uid\, provider\, user_id\, created_at\, updated_at) \
        VALUES ('1'\, 'oauth2_generic'\, 1\, NOW()\, NOW());" \
    --version 0.1.0 \
    --name gitlab-user-identities \
    --namespace c7n-system

helm install c7n/agile-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preConfig.mysql.port=3306 \
    --set preJob.preConfig.mysql.database=manager_service \
    --set preJob.preConfig.mysql.username=choerodon \
    --set preJob.preConfig.mysql.password=password \
    --set preJob.preInitDB.enable=true \
    --set preJob.preInitDB.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preInitDB.mysql.port=3306 \
    --set preJob.preInitDB.mysql.database=agile_service \
    --set preJob.preInitDB.mysql.username=choerodon \
    --set preJob.preInitDB.mysql.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/agile_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SERVICES_ATTACHMENT_URL="http://minio.amd.tonkor.com/agile-service/" \
    --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
    --set env.open.SPRING_REDIS_DATABASE=4 \
    --name agile-service \
    --version 0.10.1 \
    --namespace c7n-system

helm install c7n/test-manager-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preConfig.datasource.username=choerodon \
    --set preJob.preConfig.datasource.password=password \
    --set preJob.preInitDB.datasource.url="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/test_manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set preJob.preInitDB.datasource.username=choerodon \
    --set preJob.preInitDB.datasource.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/test_manager_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI="http://config-server.c7n-system:8010/" \
    --set env.open.SPRING_REDIS_HOST=c7n-redis.c7n-system.svc \
    --set env.open.SPRING_REDIS_DATABASE=5 \
    --name test-manager-service \
    --version 0.10.3 \
    --namespace c7n-system


helm install c7n/xwiki \
    --set env.JAVA_OPTS=-Xmx4096m \
    --set env.DB_USER=choerodon \
    --set env.DB_PASSWORD=password \
    --set env.DB_HOST=c7n-mysql.c7n-system.svc \
    --set env.DB_DATABASE=xwiki \
    --set env.OIDC_ENDPOINT_AUTHORIZATION=http://api.amd.tonkor.com/oauth/oauth/authorize \
    --set env.OIDC_ENDPOINT_TOKEN=http://api.amd.tonkor.com/oauth/oauth/token \
    --set env.OIDC_ENDPOINT_USERINFO=http://api.amd.tonkor.com/iam/v1/users/self \
    --set env.OIDC_ENDPOINT_LOGOUT=http://api.amd.tonkor.com/oauth/logout \
    --set env.CHOERODON_REQUEST_API_URL=http://api.amd.tonkor.com \
    --set env.CHOERODON_REQUEST_FRONT_URL=http://c7n.amd.tonkor.com \
    --set env.OIDC_CLIENTID=wiki \
    --set env.OIDC_SECRET=secret \
    --set env.OIDC_WIKI_TOKEN=Choerodon \
    --set persistence.enabled=true \
    --set persistence.existingClaim=wiki-pvc \
    --set service.enabled=true \
    --set ingress.enabled=true \
    --set "ingress.hosts[0]"=wiki.amd.tonkor.com \
    --timeout 3000 \
    --name xwiki \
    --version 0.10.0 \
    --namespace c7n-system  

helm install c7n/wiki-service \
    --set env.open.JAVA_OPTS="-Xms256m -Xmx512m" \
    --set preJob.preConfig.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preConfig.mysql.port=3306 \
    --set preJob.preConfig.mysql.database=manager_service \
    --set preJob.preConfig.mysql.username=choerodon \
    --set preJob.preConfig.mysql.password=password \
    --set preJob.preInitDB.mysql.host=c7n-mysql.c7n-system.svc \
    --set preJob.preInitDB.mysql.port=3306 \
    --set preJob.preInitDB.mysql.database=wiki_service \
    --set preJob.preInitDB.mysql.username=choerodon \
    --set preJob.preInitDB.mysql.password=password \
    --set env.open.SPRING_DATASOURCE_URL="jdbc:mysql://c7n-mysql.c7n-system.svc:3306/wiki_service?useUnicode=true&characterEncoding=utf-8&useSSL=false" \
    --set env.open.SPRING_DATASOURCE_USERNAME=choerodon \
    --set env.open.SPRING_DATASOURCE_PASSWORD=password \
    --set env.open.EUREKA_CLIENT_SERVICEURL_DEFAULTZONE="http://register-server.c7n-system:8000/eureka/" \
    --set env.open.EUREKA_DEFAULT_ZONE=http://register-server.c7n-system:8000/eureka/ \
    --set env.open.CHOERODON_EVENT_CONSUMER_KAFKA_BOOTSTRAP _SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_BROKERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_KAFKA_BOOTSTRAP_SERVERS="kafka-0.kafka-headless.c7n-system.svc.cluster.local:9092" \
    --set env.open.SPRING_CLOUD_STREAM_KAFKA_BINDER_ZK_NODES="zookeeper-0.zookeeper-headless.c7n-system.svc.cluster.local:2181" \
    --set env.open.SPRING_KAFKA_PRODUCER_VALUE_SERIALIZER=org.apache.kafka.common.serialization.ByteArraySerializer \
    --set env.open.SPRING_CLOUD_CONFIG_ENABLED=true \
    --set env.open.SPRING_CLOUD_CONFIG_URI=http://config-server.c7n-system:8010/ \
    --set env.open.WIKI_CLIENT=xwiki \
    --set env.open.WIKI_URL=http://wiki.amd.tonkor.com \
    --set env.open.WIKI_TOKEN=Choerodon \
    --set env.open.WIKI_DEFAULT_GROUP=XWikiAllGroup \
    --name wiki-service \
    --version 0.10.1 \
    --namespace c7n-system


```