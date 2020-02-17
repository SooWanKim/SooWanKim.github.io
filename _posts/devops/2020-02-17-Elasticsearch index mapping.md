---
layout: post
title: Elasticsearch index mapping
categories: [Devops]
---

### index mapping

index mapping이란 data의 type정의라고 보면된다. (db에서 스키마 정의)


```

{
  "mapping": {
    "properties": {
      "@message": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "@tags": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "@timestamp": {
        "type": "date"
      }
    }
  }
}

```

Kibana를 통해 Index Management에 들어가보면 indexing된 데이타들이 있고 mapping정보를 확인할 수 있다.


## Dynamaic mapping

Elastic search에서 아래 처럼 실행하고


```

PUT http://127.0.0.1:9200/customer/_doc/1
Content-Type: application/json

{
  "name": "John Doe"
}

```


```

{
  "mapping": {
    "properties": {
      "name": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      }
    }
  }
}

```

mapping 정보를 보면 자동으로 name field type이 text로 적용된것을 볼 수 있다.


***

##  Index Mapping 직접 설정


```

PUT http://localhost:5601/my_data/_mapping
Content-Type: application/json

{
  "properties" :
  {
  "timestamp":
  {
      "type" :"date"
  },
  "device_id":
  {
      "type": "long"
  },
   "device_model":{"type" :"text"},
   "os": {"type" : "keyword"},
   "app_id": {"type" : "keyword"},
   "app_version": {"type" : "keyword"},
   "uid": {"type" : "long"},
   "log":
   {
       "type": "object",
       "properties" :
       {
            "dungeon_fps":
            {
            "type": "object",
            "properties" :
            {
                "dungeon_id" : {"type" : "long"},
                "average_fps" : {"type" : "float"}
            }
       }
       }
   }
  }
}

```

log의 field type은 object이고 하이라키 밑으로 여러개의 타입을 추가할 수 있다.

mapping type [https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html)