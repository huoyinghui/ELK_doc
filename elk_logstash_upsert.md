
#### logstash upsert

使用logstash 更新doc
##### logstash: docker.elastic.co/logstash/logstash:6.2.4
##### 背景: es status 429
```bash
inserOrUpdate err: { [es_rejected_execution_exception] rejected execution of org.elasticsearch.transport.TransportService$7@438a4c8f on EsThreadPoolExecutor[name = es04_data/bulk, queue capacity = 200, org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@38e16313[Running, pool size = 8, active threads = 8, queued tasks = 200, completed tasks = 459780129]] :: {"path":"/logstash_user_install_log/update/263a43a4ace57a58_59dd39103b0b65c54110efe4fa9f2479","query":{},"body":"{\"user_info\":"},\"status\":429}"}
    at respond (/app/node_modules/elasticsearch/src/lib/transport.js:307:15)
    at checkRespForFailure (/app/node_modules/elasticsearch/src/lib/transport.js:266:7)
    at HttpConnector.<anonymous> (/app/node_modules/elasticsearch/src/lib/connectors/http.js:159:7)
    at IncomingMessage.bound (/app/node_modules/elasticsearch/node_modules/lodash/dist/lodash.js:729:21)
    at emitNone (events.js:111:20)
    at IncomingMessage.emit (events.js:208:7)
    at endReadableNT (_stream_readable.js:1064:12)
    at _combinedTickCallback (internal/process/next_tick.js:138:11)
    at process._tickDomainCallback (internal/process/next_tick.js:218:9)
  status: 429,
  displayName: 'TooManyRequests',
  message: '[es_rejected_execution_exception] rejected execution of org.elasticsearch.transport.TransportService$7@438a4c8f on EsThreadPoolExecutor[name = es04_data/bulk, queue capacity = 200, org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@38e16313[Running, pool size = 8, active threads = 8, queued tasks = 200, completed tasks = 459780129]]',
  path: '/logstash_user_install_log/update/263a43a4ace57a58_59dd39103b0b65c54110efe4fa9f2479',
  query: {},
  body: '{"user_info": ""}',
  statusCode: 429,
  response: '{"error":{"root_cause":[{"type":"es_rejected_execution_exception","reason":"rejected execution of org.elasticsearch.transport.TransportService$7@438a4c8f on EsThreadPoolExecutor[name = es04_data/bulk, queue capacity = 200, org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@38e16313[Running, pool size = 8, active threads = 8, queued tasks = 200, completed tasks = 459780129]]"}],"type":"es_rejected_execution_exception","reason":"rejected execution of org.elasticsearch.transport.TransportService$7@438a4c8f on EsThreadPoolExecutor[name = es04_data/bulk, queue capacity = 200, org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@38e16313[Running, pool size = 8, active threads = 8, queued tasks = 200, completed tasks = 459780129]]"},"status":429}',
  toString: [Function],
  toJSON: [Function] } { error: 
   { root_cause: [ [Object] ],
     type: 'es_rejected_execution_exception',
     reason: 'rejected execution of org.elasticsearch.transport.TransportService$7@438a4c8f 
     on EsThreadPoolExecutor[name = es04_data/bulk, 
     queue capacity = 200, 
     org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@38e16313[
        Running, 
        pool size = 8, 
        active threads = 8, 
        queued tasks = 200, 
     completed tasks = 459780129]]' 
    },
  status: 429 }
```

##### src data 
id 作为唯一_id
```bash
 {
    "id": "1",
    "user_info": {
      "intro": ""
    },
 }
```


#### pipeline/conf
```bash
input {
    redis { 
        host => "redis"
        port => 6379
        threads => 1
        data_type => "list"
        key => "pref:logstash_list_test_log"
    } 
}
output {
    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts => ["http://elasticsearch:9200"]
        index => "logstash_test_log"
        codec => "json"
        action => "update"
        doc_as_upsert => true
        document_id => "%{id}"
        sniffing => true
        template_overwrite => true
    }
}

```
