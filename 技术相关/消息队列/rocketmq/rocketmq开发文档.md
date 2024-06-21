## 1.docker部署rocketmq

### 注意事项

使用docker部署的时候一定要加上创建network,并且部署的时候要配置上network.

直接使用一下命令即可

```
docker network create rocketmq
# docker network ls #列出已有docker网络
# docker network inspect rocketmq #查看网络信息



docker run -d --name mqnamesrv -p 9876:9876  --network rocketmq \
-v /Users/hljy/docker/docker/volumes/rocketmq/logs/:/home/rocketmq/logs \
apache/rocketmq:5.1.3 sh mqnamesrv





docker run -d --name mqbroker -p 10911:10911 -p 10909:10909 --network rocketmq \
-v /Users/hljy/docker/docker/volumes/rocketmq/logs/:/home/rocketmq/logs \
apache/rocketmq:5.1.3 sh mqbroker -n mqnamesrv:9876 --enable-proxy autoCreateTopicEnable=true \
-c /home/rocketmq/rocketmq-5.1.3/conf/broker.conf


docker exec -it mqbroker bash -c "tail -n 10 /home/rocketmq/logs/rocketmqlogs/proxy.log"



docker run -d --name mqconsole -p 8098:8080 --network rocketmq \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=mqnamesrv:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" \
styletang/rocketmq-console-ng
```

参考文档

```
https://juejin.cn/post/7277395050508746809
```

