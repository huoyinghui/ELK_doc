##### kibana不能设置默认索引

[处理方法](https://discuss.elastic.co/t/forbidden-12-index-read-only-allow-delete-api/110282)

[可能原因](https://www.elastic.co/guide/en/elasticsearch/reference/current/disk-allocator.html)

#### 解决方法
```bash
PUT .kibana/_settings
{
  "index": {
    "blocks": {
      "read_only_allow_delete": "false"
    }
  }
```
##### api 接口
```bash
Request URL: http://localhost:5601/api/kibana/settings/defaultIndex
Request Method: POST
Status Code: 200 OK
{"value":"f4437d40-27f3-11e8-92a9-d38fb08afa7b"}
``` 

#### 正常情况查看.kibana设置如下 
```bash
GET .kibana/_settings

{
  ".kibana": {
    "settings": {
      "index": {
        "number_of_shards": "1",
        "auto_expand_replicas": "0-1",
        "blocks": {
          "read_only_allow_delete": "false"
        },
        "provided_name": ".kibana",
        "creation_date": "1520923879064",
        "number_of_replicas": "1",
        "uuid": "Q64ARHB5TWKZM39CTHH1yA",
        "version": {
          "created": "6020299",
          "upgraded": "6020399"
        }
      }
    }
  }
}
```
