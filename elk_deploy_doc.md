 **采用docker-compose编排, docker-compose.yml**
 
 ```yml
version: '3'

services:
  elasticsearch:
    image: elasticsearch:5.4.3
    container_name: elasticsearch
    restart: always
    network_mode: "bridge"
    ports:
    - "9200:9200"
    - "9300:9300"
    volumes:
    - /home/opt/elasticsearch:/usr/share/elasticsearch/data

  logstash:
    image: logstash:5.4.3
    container_name: logstash
    restart: always
    network_mode: "bridge"
    ports:
    - "5044:5044"
    - "4560:4560"
    - "8080:8080"
    volumes:
    - /home/opt/logstash/conf:/config-dir
    - /home/opt/logstash/patterns:/opt/logstash/patterns
    links:
    - elasticsearch:elasticsearch
    command: logstash -f /config-dir

  kibana:
    image: kibana:5.4.3
    container_name: kibana
    restart: always
    network_mode: "bridge"
    ports:
    - "5601:5601"
    links:
    - elasticsearch:elasticsearch
 ```
 
 当前目录下执行`docker-compose up -d`即可启动容器
 
 
 **kibana nginx配置**
 ```
    server {
        listen 80;
        server_name kibana.domain.com;
        auth_basic "basic http authorization for kibana";
        auth_basic_user_file /usr/local/nginx/conf/htpasswd.users;

        location / {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_redirect off;
            proxy_pass http://127.0.0.1:5601;
        }
    }
```

> 开启basic http authorization, 需要指定basic_user_file文件, 其内容格式`username:encrypted_password`, 其中加密过的密码串生成方式如下:

`yum -y install httpd-tools`

`htpasswd -bc /usr/local/nginx/conf/htpasswd.users username passwd`
