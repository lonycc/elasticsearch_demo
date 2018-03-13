**创建mappings**
```
PUT common
{
  "mappings": {
    "search": {
      "properties": {
        "url": {
          "type": "keyword",
          "norms": false
        },
        "thumbnail": {
          "index": false,
          "type": "keyword",
          "norms": false
        },
        "author": {
          "type": "keyword",
          "norms": false
        },
        "editor": {
            "type": "keyword",
            "norms": false
        },
        "origin": {
          "type": "keyword",
          "norms": false
        },
        "channel": {
          "type": "keyword",
          "norms": false
        },
        "website": {
          "type": "keyword",
          "norms": false
        },
        "category": {
          "type": "keyword",
          "norms": false
        },
        "has_pic": {
            "type": "boolean"
        },
        "has_video": {
            "type": "boolean"
        },
        "reserved_1": {
            "type": "keyword",
            "norms": false
        },
        "reserved_2": {
            "type": "keyword",
            "norms": false
        },
        "reserved_3": {
            "type": "keyword",
            "norms": false
        },
        "reserved_4": {
            "type": "keyword",
            "norms": false
        },
        "reserved_5": {
            "type": "keyword",
            "norms": false
        },
        "reserved_6": {
            "type": "keyword",
            "norms": false
        },
        "description": {
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart"
        },
        "tag": {
          "type": "text",
          "analyzer": "whitespace",
          "search_analyzer": "whitespace"
        },
        "title": {
          "store": true,
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart",
          "term_vector": "with_positions_offsets",
          "boost": 4,
          "fields": {
            "raw": {
              "type": "keyword"
            }
          }
        },
        "content": {
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart",
          "term_vector": "with_positions_offsets"
        },
        "pdate": {
          "type": "date",
          "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd HH:mm||yyyy-MM-dd||yyyy.MM.dd||epoch_second"
        }
      }
    }
  }
}


PUT gdszx
{
  "mappings": {
    "culture": {
      "properties": {
        "attach_id": {
          "index": false,
          "type": "keyword",
          "norms": false
        },
        "author": {
          "type": "text",
          "norms": true
        },
        "source": {
          "type": "keyword",
          "norms": false
        },
        "tag": {
          "type": "text",
          "analyzer": "whitespace",
          "search_analyzer": "whitespace"
        },
        "age": {
          "type": "keyword",
          "norms": false
        },
        "publishing": {
          "type": "keyword",
          "norms": true
        },
        "writings": {
          "type": "text",
          "norms": true
        },
        "years": {
          "type": "keyword",
          "norms": false
        },
        "times": {
          "type": "keyword",
          "norms": false
        },
        "category": {
          "type": "keyword",
          "norms": false
        },
        "location": {
          "type": "keyword",
          "norms": false
        },
        "channel": {
          "type": "keyword",
          "norms": false
        },
        "is_public": {
          "type": "boolean"
        },
        "reserved_1": {
          "type": "keyword",
          "norms": false
        },
        "reserved_2": {
          "type": "keyword",
          "norms": false
        },
        "reserved_3": {
          "type": "keyword",
          "norms": false
        },
        "reserved_4": {
          "type": "keyword",
          "norms": false
        },
        "reserved_5": {
          "type": "keyword",
          "norms": false
        },
        "reserved_6": {
          "type": "keyword",
          "norms": false
        },
        "description": {
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart"
        },
        "title": {
          "store": true,
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart",
          "term_vector": "with_positions_offsets",
          "boost": 4
        },
        "content": {
          "type": "text",
          "analyzer": "ik_smart",
          "search_analyzer": "ik_smart",
          "term_vector": "with_positions_offsets"
        }
      }
    }
  }
}
```

**创建一个分词规则, 然后在mappings上应用该规则**
```
PUT /wsb
{
  "index": {
    "analysis": {
      "analyzer": {
        "douhao": {
          "type": "pattern",
          "pattern": ","
        }
      }
    }
  }
}

POST wsb/news/_mapping
{
  "properties": {
    "channel": {
      "type": "text",
      "analyzer": "douhao",
      "search_analyzer": "douhao"
    },
    "tags": {
      "type": "text",
      "analyzer": "ik_smart",
      "search_analyzer": "ik_smart"
    },
    "docid": {
      "index": false,
      "type": "keyword",
      "norms": false
    },
    "status": {
      "index": true,
      "type": "keyword"
    },    
    "pic": {
      "index": false,
      "type": "keyword",
      "norms": false
    },
    "url": {
      "index": false,
      "type": "keyword",
      "norms": false
    },
    "activityplace": {
      "type": "keyword",
      "norms": false
    },
    "region": {
      "type": "keyword",
      "norms": false
    },
    "docsource": {
      "type": "keyword",
      "norms": false
    },
    "attr": {
      "type": "keyword",
      "norms": false
    },
    "abroad": {
      "type": "keyword",
      "norms": false
    },
    "beltandroad": {
      "type": "keyword",
      "norms": false
    },
    "friendlycity": {
      "type": "keyword",
      "norms": false
    },
    "company500": {
      "type": "keyword",
      "norms": false
    },
    "eventtime": {
      "type": "keyword",
      "norms": false
    },
    "pname": {
      "type": "text",
      "analyzer": "douhao",
      "search_analyzer": "douhao"     
    },
    "abstract": {
      "type": "text",
      "analyzer": "ik_smart",
      "search_analyzer": "ik_smart",
      "term_vector": "with_positions_offsets"
    },
    "title": {
      "store": true,
      "type": "text",
      "analyzer": "ik_smart",
      "search_analyzer": "ik_smart",
      "term_vector": "with_positions_offsets",
      "boost": 4
    },
    "pdate": {
      "type": "date",
      "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd HH:mm||yyyy-MM-dd||yyyy.MM.dd||epoch_second"
    }
  }
}
```
