#### 计算文档长度

#### 
[painless语法](https://www.elastic.co/guide/en/elasticsearch/painless/6.1/painless-specification.html)

####  [test data](https://www.elastic.co/guide/en/elasticsearch/painless/6.1/painless-examples.html)
```bash
# DELETE hockey
PUT hockey/player/_bulk?refresh
{"index":{"_id":1}}
{"first":"johnny","last":"gaudreau","goals":[9,27,1],"assists":[17,46,0],"gp":[26,82,1],"born":"1993/08/13"}
{"index":{"_id":2}}
{"first":"sean","last":"monohan","goals":[7,54,26],"assists":[11,26,13],"gp":[26,82,82],"born":"1994/10/12"}
{"index":{"_id":3}}
{"first":"jiri","last":"hudler","goals":[5,34,36],"assists":[11,62,42],"gp":[24,80,79],"born":"1984/01/04"}
{"index":{"_id":4}}
{"first":"micheal","last":"frolik","goals":[4,6,15],"assists":[8,23,15],"gp":[26,82,82],"born":"1988/02/17"}
{"index":{"_id":5}}
{"first":"sam","last":"bennett","goals":[5,0,0],"assists":[8,1,0],"gp":[26,1,0],"born":"1996/06/20"}
{"index":{"_id":6}}
{"first":"dennis","last":"wideman","goals":[0,26,15],"assists":[11,30,24],"gp":[26,81,82],"born":"1983/03/20"}
{"index":{"_id":7}}
{"first":"david","last":"jones","goals":[7,19,5],"assists":[3,17,4],"gp":[26,45,34],"born":"1984/08/10"}
{"index":{"_id":8}}
{"first":"tj","last":"brodie","goals":[2,14,7],"assists":[8,42,30],"gp":[26,82,82],"born":"1990/06/07"}
{"index":{"_id":39}}
{"first":"mark","last":"giordano","goals":[6,30,15],"assists":[3,30,24],"gp":[26,60,63],"born":"1983/10/03"}
{"index":{"_id":10}}
{"first":"mikael","last":"backlund","goals":[3,15,13],"assists":[6,24,18],"gp":[26,82,82],"born":"1989/03/17"}
{"index":{"_id":11}}
{"first":"joe","last":"colborne","goals":[3,18,13],"assists":[6,20,24],"gp":[26,67,82],"born":"1990/01/30"}
```
#### 计算文档部分字段： first+last字段长度 
```bash
GET hockey/_search
# hashMap 
POST hockey/player/_update_by_query
{
  "script": {
    "lang": "painless",
    "source": "ctx._source.row = ctx._source.last.length() + ctx._source.first.length()"
  }
}
``` 

#### 全文本长度:map类型tostr
```bash
POST hockey/player/_update_by_query
{
  "script": {
    "lang": "painless",
    "source": """
    Map m = new HashMap(); 
    m.put(1, 'aa');
    //map to str
    String ret = "";
    ctx._source.row = ret.length();
    """
  }
}

//update

POST hockey/player/_update_by_query
{
  "script": {
    "lang": "painless",
    "source": """
    Object o = ctx._source;
    ctx._source.row = o.toString();
    ctx._source.size = ctx._source.row.length();
    """
  }
}
```

#### 验证结果
```bash
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
    "total": 11,
    "max_score": 1,
    "hits": [
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "5",
        "_score": 1,
        "_source": {
          "last": "bennett",
          "size": 118,
          "assists": [
            8,
            1,
            0
          ],
          "born": "1996/06/20",
          "gp": [
            26,
            1,
            0
          ],
          "row": "{last=bennett, size=10, assists=[8, 1, 0], born=1996/06/20, gp=[26, 1, 0], row=bennettsam, first=sam, goals=[5, 0, 0]}",
          "first": "sam",
          "goals": [
            5,
            0,
            0
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "8",
        "_score": 1,
        "_source": {
          "last": "brodie",
          "size": 118,
          "assists": [
            8,
            42,
            30
          ],
          "born": "1990/06/07",
          "gp": [
            26,
            82,
            82
          ],
          "row": "{last=brodie, size=8, assists=[8, 42, 30], born=1990/06/07, gp=[26, 82, 82], row=brodietj, first=tj, goals=[2, 14, 7]}",
          "first": "tj",
          "goals": [
            2,
            14,
            7
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "10",
        "_score": 1,
        "_source": {
          "last": "backlund",
          "size": 132,
          "assists": [
            6,
            24,
            18
          ],
          "born": "1989/03/17",
          "gp": [
            26,
            82,
            82
          ],
          "row": "{last=backlund, size=14, assists=[6, 24, 18], born=1989/03/17, gp=[26, 82, 82], row=backlundmikael, first=mikael, goals=[3, 15, 13]}",
          "first": "mikael",
          "goals": [
            3,
            15,
            13
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "2",
        "_score": 1,
        "_source": {
          "last": "monohan",
          "size": 127,
          "assists": [
            11,
            26,
            13
          ],
          "born": "1994/10/12",
          "gp": [
            26,
            82,
            82
          ],
          "row": "{last=monohan, size=11, assists=[11, 26, 13], born=1994/10/12, gp=[26, 82, 82], row=monohansean, first=sean, goals=[7, 54, 26]}",
          "first": "sean",
          "goals": [
            7,
            54,
            26
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "4",
        "_score": 1,
        "_source": {
          "last": "frolik",
          "size": 129,
          "assists": [
            8,
            23,
            15
          ],
          "born": "1988/02/17",
          "gp": [
            26,
            82,
            82
          ],
          "row": "{last=frolik, size=13, assists=[8, 23, 15], born=1988/02/17, gp=[26, 82, 82], row=frolikmicheal, first=micheal, goals=[4, 6, 15]}",
          "first": "micheal",
          "goals": [
            4,
            6,
            15
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "6",
        "_score": 1,
        "_source": {
          "last": "wideman",
          "size": 131,
          "assists": [
            11,
            30,
            24
          ],
          "born": "1983/03/20",
          "gp": [
            26,
            81,
            82
          ],
          "row": "{last=wideman, size=13, assists=[11, 30, 24], born=1983/03/20, gp=[26, 81, 82], row=widemandennis, first=dennis, goals=[0, 26, 15]}",
          "first": "dennis",
          "goals": [
            0,
            26,
            15
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "1",
        "_score": 1,
        "_source": {
          "last": "gaudreau",
          "size": 130,
          "assists": [
            17,
            46,
            0
          ],
          "born": "1993/08/13",
          "gp": [
            26,
            82,
            1
          ],
          "row": "{last=gaudreau, size=14, assists=[17, 46, 0], born=1993/08/13, gp=[26, 82, 1], row=gaudreaujohnny, first=johnny, goals=[9, 27, 1]}",
          "first": "johnny",
          "goals": [
            9,
            27,
            1
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "7",
        "_score": 1,
        "_source": {
          "last": "jones",
          "size": 122,
          "assists": [
            3,
            17,
            4
          ],
          "born": "1984/08/10",
          "gp": [
            26,
            45,
            34
          ],
          "row": "{last=jones, size=10, assists=[3, 17, 4], born=1984/08/10, gp=[26, 45, 34], row=jonesdavid, first=david, goals=[7, 19, 5]}",
          "first": "david",
          "goals": [
            7,
            19,
            5
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "39",
        "_score": 1,
        "_source": {
          "last": "giordano",
          "size": 128,
          "assists": [
            3,
            30,
            24
          ],
          "born": "1983/10/03",
          "gp": [
            26,
            60,
            63
          ],
          "row": "{last=giordano, size=12, assists=[3, 30, 24], born=1983/10/03, gp=[26, 60, 63], row=giordanomark, first=mark, goals=[6, 30, 15]}",
          "first": "mark",
          "goals": [
            6,
            30,
            15
          ]
        }
      },
      {
        "_index": "hockey",
        "_type": "player",
        "_id": "3",
        "_score": 1,
        "_source": {
          "last": "hudler",
          "size": 125,
          "assists": [
            11,
            62,
            42
          ],
          "born": "1984/01/04",
          "gp": [
            24,
            80,
            79
          ],
          "row": "{last=hudler, size=10, assists=[11, 62, 42], born=1984/01/04, gp=[24, 80, 79], row=hudlerjiri, first=jiri, goals=[5, 34, 36]}",
          "first": "jiri",
          "goals": [
            5,
            34,
            36
          ]
        }
      }
    ]
  }
}
```
