# 其他可选功能

使用features选项可以用来配置其他可选功能

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
    - cassandra: true
    - chronograf: true
    - couchdb: true
    - crystal: true
    - docker: true
    - elasticsearch:
        version: 7
    - gearman: true
    - golang: true
    - grafana: true
    - influxdb: true
    - mariadb: true
    - minio: true
    - mongodb: true
    - mysql8: true
    - neo4j: true
    - ohmyzsh: true
    - openresty: true
    - pm2: true
    - python: true
    - rabbitmq: true
    - solr: true
    - webdriver: true
```

#### MariaDB {#7f9733}

启用 MariaDB 将会移除 MySQL 并安装 MariaDB . MariaDB 是 MySQL 的替代品 , 完全兼容 MySQL , 所以在应用的数据库配置中你仍然可以使用`mysql`驱动 . 

#### MongoDB {#206e37}

默认安装的 MongoDB 将会设置数据库用户名为`homestead`及对应的密码为`secret`

#### Elasticsearch {#45e23a}

如果要安装 Elasticsearch , 可以在`Homestead.yaml`文件中添加`elasticsearch`选项并指定支持的版本号 . 可以仅包含主版本 , 也可以是某个具体的版本号\(major.minor.patch\) . 默认安装会创建一个名为「homestead」的集群 . 

#### Neo4j {#4f16d2}

默认安装的 Neo4j 会设置数据库用户名为`homestead`及对应的密码`secret` . 要查看 Neo4j , 可以在浏览器中访问`http://homestead.test:7474` , Neo4j 对外提供了三个端口 :`7687`\(Bolt\)、`7474`\(HTTP\)、`7473`\(HTTPS\)用于从客户端访问 . 



