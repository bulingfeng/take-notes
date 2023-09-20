## push

该命令是把本地的镜像推送到远程仓库。默认是把镜像推送到docker的官方镜像。如果要推送到自己的仓库，则需要有指定的命名规范。

比如我的仓库名字叫bulingfeng，我提交的镜像是ubuntu，那么我必须这么写 

```dockerfile
docker push bulingfeng/ubuntu:1.3
```

如果你的镜像名字叫hello/ubuntu:1.3，那么则需要修改你的镜像为规范镜像，比如镜像名字必须为:

```dockerfile
docker tag hello/ubuntu:1.3 bulingfeng/ubuntu:1.3
```

