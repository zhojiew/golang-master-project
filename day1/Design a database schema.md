参考资料

- https://github.com/techschool/simplebank/blob/master/Makefile

使用[dbdiagram](https://dbdiagram.io/)创建pgsql表结构，并导出建表语句

```sql
Table accounts as A{
    id bigserial [pk]
    owner varchar [not null]
    balance bigint [not null]
    currency varchar [not null]
    created_at timestamptz [not null,default: `now()`]
    Indexes{
        owner
    }
}

Table entries{
    id bigserial [pk]
    accoutn_id bigint [ref: > A.id,not null]
    amount bigint [not null,note:'can be negetive or positive']
    created_at timestamptz [not null,default: `now()`]
    Indexes{
       accoutn_id 
    }
}

Table transfers{
    id bigserial [pk]
    from_account_id bigint [ref: > A.id, not null]
    to_account_id bigint [ref: > A.id,not null]
    amount bigint [not null,note:'must be positive']
    created_at timestamptz [not null,default: `now()`]
    Indexes{
        from_account_id
        to_account_id
        (from_account_id, to_account_id)
    }
}
```

![image.png](https://s2.loli.net/2023/04/09/ZAyPKj5LCMRcBqX.png)

使用postgres docker镜像启动数据库

```
$ docker pull postgres:12
```

中国大陆下载镜像较慢，可以找一个镜像加速站点配置到docker中

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://o6ul5754.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

在dockerhub的postgres网站可以找到镜像对应的初始化参数设置

> https://hub.docker.com/_/postgres

启动postgares数据库

```
$ docker run --name postgres12 -p 5432:5432 -e POSTGRES_USER=root -e POSTGRES_PASSWORD=zhojiew -d postgres:12
$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS        PORTS                                       NAMES
af63bdd1688a   postgres:12   "docker-entrypoint.s…"   2 seconds ago   Up 1 second   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres12
```

如果启动过程中出现任何故障，使用docker log查看错误日志

登入postgres数据库

```
$ docker exec -it postgres12 psql -U root
```

在vscode中安装pgsql插件

![](https://s2.loli.net/2023/04/09/rOH6pWKzlVqGUYa.png)

在数据库中执行建表语句

<img src="https://s2.loli.net/2023/04/09/n54weuGirxRYpob.png" alt="image.png" style="zoom:67%;" />

