#### es index 模版需要修改

```bash
PUT /_template/my_logs 
{
  "template": "logstash-*", 
  "order":    1, 
  "settings": {
    "number_of_shards": 1 
  },
  "mappings": {
    "_default_": { 
      "_all": {
        "enabled": false
      }
    }
  },
  "aliases": {
    "last_3_months": {} 
  }
}
```
[src](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index-templates.html)


##### 修改一个已经存在的索引
```bash
PUT /aegon_demo-2018.05.24
{
    "settings": {
        "number_of_shards" :   1,
        "number_of_replicas" : 0
    }
}
```
[src](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index-management.html)
