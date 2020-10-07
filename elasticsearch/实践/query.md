# elasticsearch查询

## index的mapping信息
```
"mappings" : {
      "properties" : {
        "device" : {
          "properties" : {
            "app_version" : {
              "type" : "keyword"
            },
            "terminal_type" : {
              "type" : "short"
            },
            "token" : {
              "type" : "keyword"
            }
          }
        },
        "location" : {
          "properties" : {
            "company" : {
              "properties" : {
                "key" : {
                  "type" : "keyword"
                },
                "location" : {
                  "type" : "geo_point",
                  "ignore_malformed" : true
                },
                "poi" : {
                  "type" : "text",
                  "fields" : {
                    "keyword" : {
                      "type" : "keyword",
                      "ignore_above" : 256
                    }
                  }
                }
              }
            },
            "home" : {
              "properties" : {
                "key" : {
                  "type" : "keyword"
                },
                "location" : {
                  "type" : "geo_point",
                  "ignore_malformed" : true
                },
                "poi" : {
                  "type" : "text",
                  "fields" : {
                    "keyword" : {
                      "type" : "keyword",
                      "ignore_above" : 256
                    }
                  }
                }
              }
            },
            "last_visit" : {
              "properties" : {
                "city_id" : {
                  "type" : "keyword"
                },
                "date" : {
                  "type" : "date"
                },
                "grid" : {
                  "properties" : {
                    "key" : {
                      "type" : "keyword"
                    },
                    "location" : {
                      "type" : "geo_point",
                      "ignore_malformed" : true
                    },
                    "poi" : {
                      "type" : "text",
                      "fields" : {
                        "keyword" : {
                          "type" : "keyword",
                          "ignore_above" : 256
                        }
                      }
                    }
                  }
                }
              }
            },
            "main_location" : {
              "properties" : {
                "city" : {
                  "type" : "keyword"
                },
                "country" : {
                  "type" : "keyword",
                  "doc_values" : false
                },
                "county" : {
                  "type" : "keyword"
                },
                "province" : {
                  "type" : "keyword"
                }
              }
            },
            "other_locations" : {
              "properties" : {
                "key" : {
                  "type" : "text",
                  "fields" : {
                    "keyword" : {
                      "type" : "keyword",
                      "ignore_above" : 256
                    }
                  }
                },
                "location" : {
                  "properties" : {
                    "lat" : {
                      "type" : "float"
                    },
                    "lon" : {
                      "type" : "float"
                    }
                  }
                },
                "poi" : {
                  "type" : "text",
                  "fields" : {
                    "keyword" : {
                      "type" : "keyword",
                      "ignore_above" : 256
                    }
                  }
                }
              }
            }
          }
        },
        "uid" : {
          "type" : "keyword"
        }
      }
    }
```
## 查询和过滤语句
```
GET profile/_search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "location.main_location.county": "海淀区"
        }
      }, 
      "must": [
        {"match": {
          "location.company.poi": "*万寿路西街*"
        }}
      ]
    }
  }
}

```
## 复合查询
一个复合查询中可以包括bool query，boosting query，constant_score query，dis_max query和function_score query

### bool query
一个bool查询中，发生(occurrence)的类型有must\filter\should\must_not
```
GET profile/_count
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "location.company.poi": "万寿路西街"
        }}
      ],
      "filter": {
        "term": {
          "location.main_location.county": "海淀区"
        }
      },
      "should": [
        {"range": {
          "location.last_visit.date": {
            "gte": "2020-07-19",
            "lte": "2020-10-11"
          }
        }}
      ],
      "must_not": [
        {"range": {
          "location.last_visit.date": {
            "lte":"2020-01-11"
          }
        }}
      ],
      "minimum_should_match": 1,
      "boost": 1
    }
  }
}

```