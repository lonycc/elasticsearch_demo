**索引并查询一个文档, 指定id**
```
PUT /demo/external/1
{
  "name": "John Doe"
}
```

**索引并查询一个文档, 不指定id, 默认会递增**
```
PUT /demo/external
{
  "name": "fanny"
}
```

**删除文档**
`DELETE /demo/external/1`

**获取指定文档**
`GET /demo/external/1`


**更新文档**
```
POST /demo/external/1/_update
{
  "doc": { 
    "name": "Jane Doe" 
  }
}
```

**更新文档, 使用script**
```
POST /demo/external/1/_update
{
  "script": "ctx._source.age += 5"
}
```

**更新文档, 使用script**
```
POST /demo/external/1/_update
{
  "script": "ctx._source.name_of_new_field" = \"value_of_new_field\""
}
```

**更新文档, 使用script**
```
POST /demo/external/1/_update
{
  "script":"ctx._source.remove(\"name_of_field\")"
}
```

**批量导入文档**
`POST /_bulk --data-binary @data2.json`
 

**查询并删除文档**
```
DELETE /demo/external/_query
{
  "query": {
    "match": { "name": "John" }
  }
}
```

**查询接口**
`GET /{index}/{type}/_search`



**分析器, 常用分词器ik_smart/ik_max_word/standard/english/whitespace**
```
GET /_analyze
{
  "text": "蓝瘦香菇",
  "analyzer": "ik_smart"
}
```


**标题中至少匹配两个单词的才返回**
```
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title": "brown" }},
        { "match": { "title": "fox"   }},
        { "match": { "title": "dog"   }}
      ],
      "minimum_should_match": 2 
    }
  }
}
```

**合并查询**
```
{
  "query": {
    "bool": {
      "must":     { "match": { "title": "quick" }},
      "must_not": { "match": { "title": "lazy"  }},
      "should": [
            { "match": { "title": "brown" }},
            { "match": { "title": "dog"   }}
      ]
    }
  }
}
```

**精度控制,至少两个单词匹配**
```
{
  "query": {
    "match": {
      "title": {
        "query": "quick brown dog",
        "minimum_should_match": "75%"
      }
    }
  }
}
```

**与逻辑查询**
```
{
    "query": {
        "match": {
            "title": {      
                "query":    "BROWN DOG!",
                "operator": "and"
            }
        }
    }
}
```

**接口语法**
`curl -X[METHOD] http://localhost:9200/<index>/<type>/_query?pretty -d '{}'`

**对content字段做匹配, 会分词**
```
{
  "query": {
    "match": {
        "content" : {
            "query" : "我的宝马多少马力"
        }
    }
  }
}
```

**严格匹配所有分词词汇, 用match_phrase**
```
{
  "query": {
    "match_phrase": {
        "content" : {
            "query" : "我的宝马多少马力"
        }
    }
  }
}
```

**至少匹配一个分词词汇, 用slop**
```
{
  "query": {
	"match_phrase": {
		"content" : {
			"query" : "我的宝马多少马力",
			"slop" : 1
		}
	}
  }
}
```

**分别对title和content字段匹配**
```
{
  "query": {
    "multi_match": {
        "query" : "我的宝马多少马力",
        "fields" : ["title", "content"]
    }
  }
}
```

**加上匹配权重, 完全匹配的评分最高, 其他乘以0.3权重**
```
{
  "query": {
    "multi_match": {
      "query": "我的宝马发动机多少",
      "type": "best_fields",
      "fields": [
        "tags",
        "content"
      ],
      "tie_breaker": 0.3
    }
  }
}
```

**越多字段匹配评分越高**
```
{
  "query": {
    "multi_match": {
      "query": "我的宝马发动机多少",
      "type": "most_fields",
      "fields": [
        "tags",
        "content"
      ]
    }
  }
}
```

**词条分词词汇分配到不同字段, 使用cross_fields**
```
{
  "query": {
    "multi_match": {
      "query": "我的宝马发动机多少",
      "type": "cross_fields",
      "fields": [
        "tags",
        "content"
      ]
    }
  }
}
```

**不分词, 完全匹配, 用term**
```
{
  "query": {
    "term": {
      "content": "汽车保养"
    }
  }
}
```

**指定权重, 用boost**
```
{ 
    "query": {
        "term": {
            "title": {
                "value":"crime",
                "boost":10.0
             }
        }
    }
}
```

**多term查询, tags中包含novel或book**
```
{ 
    "query": {
        "terms": {
            "tags": ["novel","book"]
        }
    }
}
```

**常用词查询, 低于cutoff_frequency的词出现在低频组**
```
{ 
    "query": {
        "common": {
             "title":{
                 "query":"crime and punishment",
                 "cutoff_frequency":0.001
             }
        }
    }
}
```

**逻辑查询, 使用operator**
```
{ 
    "query": {
        "match": {
            "title": {
                 "query":"crime and punishment",
                 "operator":"and"
            }
        }
    }
}
```

**前缀查询**
```
{ 
    "query": {
        "match_phrase_prefix": {
            "title": {
                 "query":"crime  punish",
                 "slop":1,
                 "max_expansions":20
            }
        }
    }
}
```

**通配符查询, 支持*和?**
```
{ 
    "query": {
        "wildcard": {
             "title": "cr?me"
        }
    }
}
```

**范围查询**
```
{ 
    "query": {
        "range": {
             "year": {
                  "gte" :1890,
                  "lte":1900
              }
        }
    }
}
```

**正则查询**
```
{ 
    "query": {
        "regexp": {
             "title": {
                  "value" :"cr.m[ae]",
                  "boost":10.0
              }
        }
    }
}
```

**布尔查询**
```
{
    "query": {
        "bool": {
            "must": {
                "term": {
                    "title": "crime"
                }
            }, 
            "should": {
                "range": {
                    "year": {
                        "from": 1900, 
                        "to": 2000
                    }
                }
            }, 
            "must_not": {
                "term": {
                    "otitle": "nothing"
                }
            }
        }
    }
}
```

**查询并更新**
```
POST common/search/_update_by_query
{
  "script": {
    "source": "ctx._source['thumbnail'] = 'http://www.domain.com/'"
  },
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "has_video": true
          }
        },
        {
          "term": {
            "has_pic": false
          }
        },
        {
          "term": {
            "category": "demo"
          }
        }
      ]
    }
  }
}
```

**多条件查询**
```
GET /common/search/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "has_video": true
          }
        },
        {
          "term": {
            "has_pic": false
          }
        },
        {
          "term": {
            "category": "demo"
          }
        }        
      ]
    }
  }
}
```

> 包含全部关键词match_all,包含任意一个关键词match，不包含以下关键词exclude

**返回结果**
```
POST /twitter/tweet/_search?scroll=1m
{
    "size": 100,
    "query": {
        "match" : {
            "title" : "elasticsearch"
        }
    }
}
```


> query_then_fetch  限制深度分页，也就是说不允许limit 10000, 100这样, 最多只能翻个50页这样

**初始化**
```
POST /my_index/my_type/_search?scroll=1m
{
	"query": { "match_all": {}}
}
返回一个_scroll_id, _scroll_id用来下次取数据用
```

**遍历(可迭代)**
```
POST /_search?scroll=1m
{
	"scroll_id": "一个hash过的字符串"
}
```

**不关注排序**
```
POST /my_index/my_type/_search?search_type=scan&scroll=1m&size=50
{
	"query": { "match_all": {}}
}
```

**查看上下文个数**
`GET /_nodes/stats/indices/search?pretty`

**清除scroll api**
```
DELETE /_search/scroll
{
	"scroll_id": ["hash过的字符串"]
}

DELETE /_search/scroll/_all
```

> [aggregrations](https://www.cnblogs.com/ghj1976/p/5311183.html)

> Metrics(指标), Metrics 是简单的对过滤出来的数据集进行avg,max等操作，是一个单一的数值。

> Bucket(桶), Bucket 你则可以理解为将过滤出来的数据集按条件分成多个小数据集，然后Metrics会分别作用在这些小数据集上。


**聚合中添加指标metric**
```
GET /cars/transactions/_search
{
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": {
               "avg": {
                  "field": "price"
               }
            }
         }
      }
   }
}
```

**buckets inside buckets, 聚合分层级, 每种颜色的平均价格**
```
GET /cars/transactions/_search?search_type=count
{
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": {
               "avg": {
                  "field": "price"
               }
            },
            "make": {
                "terms": {
                    "field": "make"
                }
            }
         }
      }
   }
}
```

**聚合和查询在一个作用域里，不指定查询query，相当于match_all**
```
GET /cars/transactions/_search?search_type=count
{
    "aggs" : {
        "colors" : {
            "terms" : {
              "field" : "color"
            }
        }
    }
}
```


**price>10000 并且对这些结果聚合求平均price**
```
GET /cars/transactions/_search?search_type=count
{
    "query" : {
        "filtered": {
            "filter": {
                "range": {
                    "price": {
                        "gte": 10000
                    }
                }
            }
        }
    },
    "aggs" : {
        "single_avg_price": {
            "avg" : { "field" : "price" }
        }
    }
}
```

**filter bucket, 只过滤聚合结果, 过去一个月内平均价格**
```
GET /cars/transactions/_search?search_type=count
{
   "query":{
      "match": {
         "make": "ford"
      }
   },
   "aggs":{
      "recent_sales": {
         "filter": {
            "range": {
               "sold": {
                  "from": "now-1M"
               }
            }
         },
         "aggs": {
            "average_price":{
               "avg": {
                  "field": "price"
               }
            }
         }
      }
   }
}
```

**post filter, 只过滤搜索结果, 不过滤聚合**
```
GET /cars/transactions/_search?search_type=count
{
    "query": {
        "match": {
            "make": "ford"
        }
    },
    "post_filter": {
        "term" : {
            "color" : "green"
        }
    },
    "aggs" : {
        "all_colors": {
            "terms" : { "field" : "color" }
        }
    }
}
```
