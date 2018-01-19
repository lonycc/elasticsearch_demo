# 搜索建议和补全的demo

## 1. 搜索建议

**创建索引**
```
PUT /suggest
{
  "index": {
    "analysis": {
      "filter": {
        "autocomplete_filter": {
          "type": "edge_ngram",
          "min_gram": 1,
          "max_gram": 15
        },
        "pinyin_first_letter_and_full_pinyin_filter": {
          "type": "pinyin",
          "keep_first_letter": true,
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_none_chinese": false,
          "keep_original": false,
          "limit_first_letter_length": 16,
          "lowercase": true,
          "trim_whitespace": true,
          "keep_none_chinese_in_first_letter": true
        },
        "full_pinyin_filter": {
          "type": "pinyin",
          "keep_first_letter": true,
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_none_chinese": false,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "lowercase": true,
          "trim_whitespace": true,
          "keep_none_chinese_in_first_letter": true
        }
      },
      "analyzer": {
        "full_prefix_analyzer": {
          "type": "custom",
          "char_filter": [
            "html_strip"
          ],
          "tokenizer": "keyword",
          "filter": [
            "lowercase",
            "full_pinyin_filter",
            "autocomplete_filter"
          ]
        },
        "chinese_analyzer": {
          "type": "custom",
          "char_filter": [
            "html_strip"
          ],
          "tokenizer": "keyword",
          "filter": [
            "lowercase",
            "autocomplete_filter"
          ]
        },
        "pinyin_analyzer": {
          "type": "custom",
          "char_filter": [
            "html_strip"
          ],
          "tokenizer": "keyword",
          "filter": [
            "pinyin_first_letter_and_full_pinyin_filter",
            "autocomplete_filter"
          ]
        }
      }
    }
  }
}
```

# 创建mappings
```
POST suggest/news/_mapping
{
  "properties": {
    "title": {
      "type": "text",
      "analyzer": "full_prefix_analyzer"
    },
    "url": {
      "type": "keyword",
      "norms": false
    },
    "website": {
      "type": "keyword",
      "norms": false
    }
  }
}
```

**录入数据**
```
PUT suggest/news/1?refresh
{
  "title": "我们都有一个家名字叫中国"
}
PUT suggest/news/2?refresh
{
  "title": "兄弟姐妹都很多景色也不错"
}
```

**测试分词器效果**
```
GET suggest/_analyze
{
	"text": "刘德华AT2016",
	"analyzer": "full_prefix_analyzer"
}
```

## 2. 搜素补全

**创建索引, 用于实现补全**
```
PUT suggest
{
  "mappings": {
    "news": {
      "properties": {
        "suggest": {
          "type": "completion",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart",
          "preserve_position_increments": false,
          "preserve_separators" : false,
          "max_input_length" : 50
        },
        "title": {
          "type": "keyword"
        }
      }
    }
  }
}
```

**统一分配权重**
```
PUT suggest/news/1?refresh
{
    "suggest" : {
        "input": [ "中国经济", "中国文化", "中国电影", "中华人民共和国"],
        "weight" : 34
    },
    "title": "中国" //title可空
}
```

**单独分配权重**
```
PUT suggest/news/2?refresh
{
    "suggest" : [
        {
            "input": "Nevermind",
            "weight" : 10
        },
        {
            "input": "Nirvana",
            "weight" : 3
        }
    ],
	"title": "nir"
}
```

**默认权重**
```
PUT suggest/news/3?refresh
{
    "suggest" : [ "tonywang", "tonyli" ],
    "title": "tony"
}
```

**执行查询**
```
POST suggest/_search?pretty
{
    "suggest": {
        "suggest" : {
            "text|prefix" : "中国",  //查询模式可选text和prefix
            "completion" : { 
                "field" : "suggest",
                "size" : 5,
                "fuzzy" : {
                  "fuzziness": 2
                }
            }
        }
    }
}
```

**python实现**
```
es_suggest_options = {
    "suggest": {
        "prefix": keyword,
        "completion": {
            "field": "suggest",
            "size": 10
        }
    }
}
try:
    client = Elasticsearch(hosts=['127.0.0.1:9200'], timeout=30000)
    result = es_client.suggest(index='suggest', body=es_suggest_options)
    return {'success': 1, 'data': result}, 200
except Exception as e:
    return {'success': 0, 'message': e}, 200
```
