#### elk5.6升级6.2 多个type类型合并
```bash
背景:

0-14: 数据type->log_session
14-now: 数据type->doc默认类型
```

#### 处理过程
```bash
step1: log_sesssion 的文档为578756不再增加
GET log_session-2018.04.12/log_session/_search
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 578756
  },

}
所以优先处理，同时把旧的类型记录到ctx._source.type中
#doc || log_session --> doc
POST _reindex
{
  "source": {
    "index": "log_session-2018.04.12",
    "type": ["log_session"]
  },
  "dest": {
    "index": "log_session-2018.04.12_doc"
  },
  "script": {
    "source": """
      ctx._source.type = ctx._type; 
      ctx._type = 'doc';
    """
  }
}
处理14后的数据，应该先停止logstash对改索引的修改，(数据缓存在消息队列中)，reindex
POST _reindex
{
  "source": {
    "index": "log_session-2018.04.12",
    "type": ["doc"]
  },
  "dest": {
    "index": "log_session-2018.04.12_doc"
  },
  "script": {
    "source": """
      ctx._source.type = ctx._type;
      ctx._type = 'doc';
    """
  }
}


转移厚效果:
GET log_session-2018.04.12_doc/_search ---> 总数 1373191
```
#### 心得
```bash
命名应该兼容之前的索引，以免影响业务.
```