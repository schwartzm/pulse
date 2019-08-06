# pulse
The pulse of things. An experiment in using the Google sentiment API. Find freely available resources on the web that are mostly subjective in nature, feed them to the sentiment API, and get an average. Generally, limit each sentiment ("score") to a "channel" of life, so the score is based on related information. Think of it as sentiment on scoped aggregations of subjective data. The subjective part is an important aspect. The data to be checked for sentiment will not include things such as API documentation, technical whitepapers, how-to guides, IETF standards proposals, etc. Rather, the data should be things like news headlines, user posts, op-ed, etc.

## Reinventing the Wheel?
Yes, this has been done. But, it's the process of designing and building it that's fun. The result might be interesting to look at, but it's more about the dev journey.

## Functional
The sentiment results will be exposed by a readonly REST API (like, GET /pulse/{channel}). Currently, no plans for an exposed CRUD interface. To control cost and stay within free tiers on hosting and sentiment API calls, the API will be consumed only by a web page served by this app.

## Channel Configuration
Since there can be many channels over which sentiment will be measured, the details of each channel must be configured.

General idea for retaining channel configuration...

```
{
  "channels" : {
    "channel_x": {
      "source_a": {
        "url": "https://example.com/rss",
        "format": "rss"
        "extract_fields": ["title", "summary"]
      },
      "source_b": {
        "url": "https://example.net/posts/",
        "format": "csv",
        "extract_fields": ["c_1", "c_3"] // Example idea. c_1 == column 1.
      }
    },
    "channel_y": {
      // sources    
    }
}
```

## Sentiment Collection Process Overview
On a schedule (e.g., once per day), the fetch, measure, average, and save process will execute in the background. The results of the process will be available to the exposed readonly REST interface for serving up to clients.

For each configured channel:
1. Fetch the source(s)
2. For each source, extract the information to submit to the sentiment API
3. Average the sentiment results (measurements). Overall, this is the pulse of the specific channel.
4. Save the pulse to a persistent storage for the given channel; timestamped so that a time series can accrue.

Many details are not yet finalized. For example, in #2, exactly which information is extracted? If every source is just slightly different, even with same payload format, how to make the extraction *easily* extensible and apples-to-apples?

TODO:
* Does the sentiment API work best with short snippets or longer text?
* How are sentiment API charges incurred (call count, message size, etc.)?

## Pulse Read API
The readonly pulse read API (e.g., GET /pulse/{channel}) will return the pulse for all channels, or a specific channel. Optional parameters to the root API will be introduced to affect the data returned.

`GET /pulse/` Returns the most recent sentiment data for all available channels.

Example payload:
```
{
  "channels" : {
    "channel_x": {
      "measure": 7, // TBD, the datatype/units of sentiment measurement
      "updated": "2018-01-02T23:11:42",
      "links": { // HATEOS compliance
        "rel": "self",
        "href": "/pulse/channel_x",
        "type": "GET"        
      }
    },
    "channel_y": {
      "measure": 7,
      "updated": "2018-01-02T23:11:44",
      "links": {
        "rel": "self",
        "href": "/pulse/channel_y",
        "type": "GET"        
      }
    }
  },
}
```

`GET /pulse/channel_y` Returns the most recent sentiment data for channel "channel_y".

Example payload:
```
{
  "channels" : {
    "channel_y": {
      "measure": 7,
      "updated": "2018-01-02T23:11:42"
      "links": {
        "rel": "self",
        "href": "/pulse/channel_y",
        "type": "GET"
      }
    }
  },
}
```
TODO:
As far as REST best practices go, should a single-item list not be returned for a single resource. Rather, just channel_x not in a list of channels.

`GET /pulse/?channel=channel_x|channel_w|channel_a` Returns the most recent sentiment data for channels x, w, and a. Order not guaranteed.

TODO:
Determine best practice REST API for specifying a subset of resources to return. Not sure if a paramter list is correct (like above), or something like /pulse/channel_x/channel_w/channel_a or /pulse/channel_x,channel_w,channel_a or `GET /pulse/?c=[channel_x,channel_w,channel_a]`.







