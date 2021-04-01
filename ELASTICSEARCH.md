## Eslasticsearch and Twint

### Run ELK via Docker

`docker pull docker.elastic.co/elasticsearch/elasticsearch:7.12.0`

`docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.12.0`

### Create ELK index yourself as Twint is buggy
Cf. https://github.com/twintproject/twint/issues/1040#issuecomment-737359393

```sh
curl -X DELETE "localhost:9200/twinttweets?pretty" -H 'Content-Type: application/json'

curl -X PUT "localhost:9200/twinttweets?pretty" -H 'Content-Type: application/json' -d '
{
    "settings": {
        "analysis": {
            "normalizer": {
                "hashtag_normalizer": {
                    "filter": [
                        "lowercase",
                        "asciifolding"
                    ],
                    "type": "custom",
                    "char_filter": []
                }
            }
        }
    }
}'



curl -X PUT "localhost:9200/twinttweets/_mapping?pretty" -H 'Content-Type: application/json' -d'
{
    "dynamic": false,
    "properties": {
        "cashtags": {
            "type": "keyword",
            "normalizer": "hashtag_normalizer"
        },
        "conversation_id": {
            "type": "long"
        },
        "created_at": {
            "type": "text"
        },
        "date": {
            "type": "date",
            "format": "yyyy-MM-dd HH:mm:ss"
        },
        "day": {
            "type": "integer"
        },
        "essid": {
            "type": "keyword"
        },
        "geo_near": {
            "type": "geo_point"
        },
        "geo_tweet": {
            "type": "geo_point"
        },
        "hashtags": {
            "type": "keyword",
            "normalizer": "hashtag_normalizer"
        },
        "hour": {
            "type": "integer"
        },
        "id": {
            "type": "long"
        },
        "lang": {
            "type": "keyword"
        },
        "language": {
            "type": "text",
            "fields": {
                "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                }
            }
        },
        "link": {
            "type": "text"
        },
        "location": {
            "type": "keyword"
        },
        "mentions": {
            "type": "nested",
            "properties": {
                "id": {
                    "type": "long"
                },
                "name": {
                    "type": "text"
                },
                "screen_name": {
                    "type": "text"
                }
            }
        },
        "name": {
            "type": "text"
        },
        "near": {
            "type": "text"
        },
        "nlikes": {
            "type": "integer"
        },
        "nreplies": {
            "type": "integer"
        },
        "nretweets": {
            "type": "integer"
        },
        "photos": {
            "type": "text"
        },
        "profile_image_url": {
            "type": "text"
        },
        "quote_url": {
            "type": "text"
        },
        "reply_to": {
            "type": "nested",
            "properties": {
                "id": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "name": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "screen_name": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "user_id": {
                    "type": "keyword"
                },
                "username": {
                    "type": "keyword"
                }
            }
        },
        "retweet": {
            "type": "text"
        },
        "retweet_date": {
            "type": "date",
            "format": "yyyy-MM-dd HH:mm:ss",
            "ignore_malformed": true
        },
        "retweet_id": {
            "type": "keyword"
        },
        "search": {
            "type": "text"
        },
        "source": {
            "type": "keyword"
        },
        "thumbnail": {
            "type": "text"
        },
        "timezone": {
            "type": "keyword"
        },
        "trans_dest": {
            "type": "keyword"
        },
        "trans_src": {
            "type": "keyword"
        },
        "translate": {
            "type": "text"
        },
        "tweet": {
            "type": "text"
        },
        "urls": {
            "type": "keyword"
        },
        "user_id_str": {
            "type": "keyword"
        },
        "user_rt": {
            "type": "keyword"
        },
        "user_rt_id": {
            "type": "keyword"
        },
        "username": {
            "type": "keyword",
            "normalizer": "hashtag_normalizer"
        },
        "video": {
            "type": "integer"
        }
    }
}'
```

### Run Twint

`twint -s "beta.gouv" --limit 100 --elasticsearch localhost:9200`

### Check that tweets are in ELK

Navigate to `https://localhost:9200/_search/`