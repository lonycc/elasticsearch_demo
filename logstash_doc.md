**一份logstash配置文件**

```
input {
    file {
        path => "/usr/local/etc/nginx/logs/access.log"
        type => "nginx_log"
        start_position => beginning
        ignore_older => 0
        sincedb_path => "/dev/null"
    }
}
filter {
    grok {
        "message" => '^(?<remote_addr>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}) - ((\s+)|-) \[(?<time_local>.*)\] "(?<request_method>POST|GET) (?<request_url>[^ ]+) ([^"]*)" (?<status>\d+)'
    }
    date {
        match => [ "time_local" , "dd/MMM/YYYY:HH:mm:ss Z" ]
        locale => "cn"
    }
    mutate {
        convert => ["status", "integer"]
    }
}
output {
    elasticsearch {
        hosts => "localhost"
    }
    stdout {}
}
```

执行 `logstash agent -f ~/logstash/logstash.conf` 通过kibana查看数据
