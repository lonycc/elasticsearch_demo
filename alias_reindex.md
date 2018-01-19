**索引别名, 为以后重新索引做准备**

```
PUT /common_v1  #创建索引common_v1

PUT /common_v1/_alias/common #让别名common指向common_v1

GET /*/_alias/common  #查看别名common指向的索引

GET /common_v1/_alias/*  #查看索引common_v1有哪些别名

PUT /common_v2  #创建新索引
```

**查询拿出数据**

```
GET /common/_search?scroll=1m 
{
   "query": {
        "range": {
            "pdate": {
                "gte":"2008-01-01",
                "lt":"2009-01-01"
            }
        }
   },
   "sort": ["_doc"],
   "size": 10000
}
```

**利用elasticsearch提供的reindex接口将数据从旧的索引导入新的索引**
```
# coding=utf-8

from elasticsearch import Elasticsearch
import elasticsearch.helpers
import datetime
from time import sleep
import sys

client = Elasticsearch(['127.0.0.1:9200'], sniffer_timeout=360000)
target_client = client

begin = datetime.date(2000, 1, 1)
end = datetime.date(2017, 11, 21)

d = begin
delta = datetime.timedelta(days=1)

while d <= end:
	begin_date = d.strftime("%Y-%m-%d")
	d += delta
	end_date = d.strftime("%Y-%m-%d")
	print('now ' + begin_date)
	try:
		query = {"query":{"range":{"pdate":{"gte":begin_date,"lt":end_date}}}}
		elasticsearch.helpers.reindex(client, 'common_v1', 'common_v2', query=query, target_client=target_client, chunk_size=10000, scroll=u'10m')
	except Except as e:
		print('timeout')
		sys.exit(0)

print('finished')
```

**别名指向新索引, 删除旧索引**
```
POST /_aliases
{
    "actions": [
        { "remove": { "index": "common_v1", "alias": "common" }},
        { "add":    { "index": "common_v2", "alias": "common" }}
    ]
}
```
