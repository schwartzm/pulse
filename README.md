# pulse
The pulse of things. An experiment in using the Google sentiment API. Find freely available resources on the web that are mostly subjective in nature, feed them to the sentiment API, and get an average. Generally, limit each sentiment ("score") to a "channel" of life, so the score is based on related information. Think of it as sentiment on scoped aggregations of subjective data. The subjective part is an important aspect. The data to be checked for sentiment will not include things such as API documentation, technical whitepapers, how-to guides, IETF standards proposals, etc. Rather, the data should be things like news headlines, user posts, op-ed, etc.

## Reinventing the Wheel?
Yes, this has been done. But, it's the process of designing and building it that's fun. The result might be interesting to look at, but it's more about the dev journey.

## Functional
The sentiment results will be exposed by a readonly REST API (like, GET /pulse/{channel}). Currently, no plans for an exposed CRUD interface. To control cost and stay within free tiers on hosting and sentiment API calls, the API will be consumed only by a web page served by this app.

## Channel Configuration
Since there can be many channels over which sentiment will be measured, the details of each channel must be configured.
```
{
  "channels" : [
    {
    
    },
    {
    
    }    
  
  ]

}
```




