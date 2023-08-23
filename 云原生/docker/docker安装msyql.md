```dockerfile
docker pull mysql:8.1.0

docker run -p 3306:3306 --name mysql_test -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.1.0
```

## 解决Navicat mysql无法连接访问问题

```
docker exec -it mysql_test bash;

mysql -u root -p123456;

use mysql;
alter user 'root' identified with mysql_native_password by '123456';
flush privileges;

# 重启容器
docker stop mysql_test;
docker start mysql_test;
```

