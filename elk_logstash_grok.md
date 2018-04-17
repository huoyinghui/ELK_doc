#### logstash 解析
[grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)

[参考](https://anbc.gitbooks.io/elk-handbook/content/di_qi_jie_codec_de_pei_zhi.html)

[匹配模式](https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/grok-patterns)

日志:
```bash
142.44.214.201 [17/Apr/2018:10:08:19 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
```
模式
```bash
%{IP:client} \[%{DATA:time}\] \"%{WORD:method} %{DATA:request} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:user_agent}\" \"%{DATA:http_x_forwarded_for}\"
```
[测试网址](http://grokdebug.herokuapp.com/)

结果
```bash
{
  "client": [
    "142.44.214.201"
  ],
  "IPV6": [
    null
  ],
  "IPV4": [
    "142.44.214.201"
  ],
  "time": [
    "17/Apr/2018:10:08:19 +0000"
  ],
  "method": [
    "GET"
  ],
  "request": [
    "/"
  ],
  "http_version": [
    "1.1"
  ],
  "BASE10NUM": [
    "1.1",
    "304",
    "0"
  ],
  "response_code": [
    "304"
  ],
  "body_sent_bytes": [
    "0"
  ],
  "referrer": [
    "-"
  ],
  "user_agent": [
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36"
  ],
  "http_x_forwarded_for": [
    "-"
  ]
}
```


在线测试，找到准确的数据匹配模式。
#### 预览
![image](https://raw.githubusercontent.com/hyhlinux/static/master/img/grokdebug.jpg)

#### logstash 过滤
```bash
filter {
    geoip {
        source => "clientip"
        target => "geoip"
        database => "/test/logstash-5.0.0/GeoLite2-City.mmdb"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
    }

    mutate {
        convert => [ "[geoip][coordinates]", "float"]
          }
    }
}
```
#### nginx 解析
```bash
input {
    redis {
        data_type => "list"
        key => "nginx-log"
        host => "127.0.0.1" 
        port => 6379
        # type => "nginx-access"
        add_field => {
            host => "%{[beat][hostname]}"
        }
    }
}
```

```bash
filter {
    if [type] == "nginx-access" {
        drop {
        percentage => 50
        }
        grok {
            match => { "message" => ["%{HOSTNAME:domain} %{IPORHOST:remote_ip} \[%{DATA:time}\] \"%{WORD:method} %{DATA:request} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:user_agent}\" \"%{DATA:http_x_forwarded_for}\" %{GREEDYDATA:upstream_response_time}"] }
            remove_field => "message"
        }
        mutate {
            convert => { "body_sent_bytes" => "integer" }
            convert => { "upstream_response_time" => "float" }
        }
    }

    if [fields][log_topics] == "nginx-error" {
        grok {
            match => { "message" => ["(?<timestamp>\d{4}/\d{2}/\d{2} \d{2}:\d{2}:\d{2}) \[%{DATA:err_severity}\] (%{NUMBER:pid:int}#%{NUMBER}: \*%{NUMBER}|\*%{NUMBER}) %{DATA:err_message}(?:, client: (?<client_ip>|%{HOSTNAME}))(?:, server: %{IPORHOST:server})(?:, request: %{QS:request})?(?:, host: %{QS:client_ip})?(?:, upstream: \"%{URI:upstream})?"] }
            remove_field => "message"
        }
    }
}
```

```bash
output {
    if [fields][log_topics] == "nginx-access" {
        # stdout {
        #     codec => rubydebug   
        # }
        elasticsearch {
            hosts => ["localhost:9200", "localhost:9200"]
            index => "nginx-access-log-%{+YYYY.MM.dd}"
            template_overwrite => true
        }
    }

    
    if [fields][log_topics] == "nginx-error" {
        # stdout {
        #     codec => rubydebug   
        # }
        elasticsearch {
            hosts => ["localhost:9200"]
            index => "nginx-error-log-%{+YYYY.MM.dd}"
            template_overwrite => true
        }
    }
}
```
