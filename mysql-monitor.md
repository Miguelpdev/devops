# Mysql

### Crear archivo .my.cnf

Host del servidor bd mysql

```
[client]
host=172.16.*.*
user=root
password=test
```

### Dockerfile

```
docker run -d -p 9104:9104 -v $(pwd)/.my.cnf:/cfg/.my.cnf prom/mysqld-exporter --config.my-cnf=/cfg/.my.cnf
```

### O Crear archivo docker-compose.yml

```
version: '3'

services:
  mysql-exporter:
    image: quay.io/prometheus/mysqld-exporter
    command:
      - "--collect.global_status"
      - "--collect.info_schema.innodb_metrics"
      - "--collect.auto_increment.columns"
      - "--collect.info_schema.processlist"
      - "--collect.binlog_size"
      - "--web.listen-address=0.0.0.0:9104"
      - "--web.telemetry-path=/metrics"
      - "--config.my-cnf=/etc/my.cnf"
    environment:
      DATA_SOURCE_NAME: "root:1234@mysql:3306/bd?tls=false"
    ports:
      - "9104:9104"
    volumes:
      - "./my.cnf:/etc/my.cnf"
```

### Agregar en prometheus.yml

```
  - job_name: 'mysql-test'
    metrics_path: '/metrics'
    static_configs:
    - targets: ['172.16.*.*:9104']
```

### Dasboard grafana mysqld exporter

```
14057
```
