# pulse
The pulse of things. An experiment in using the Google sentiment API. In summary, sources of freely available text resources on the web will be fed to the Google sentiment API to get a measure of sentiment. For text within the same subject area, an average will be calculated. Since "average sentiment" sounds boring, the average sentiment value for a given subject area, or domain of thought, will be called the "pulse".

Sentiment will be meaused for texts within certain domains of thought. Oxford provides one definition of "domain" as "A specified sphere of activity or knowledge." ([source](https://www.lexico.com/en/definition/domain "Oxford definition")). Example domains are sports, automobiles, consumer products, news. Or, domains can be more specific, such as U.S. football, Ford Pinto, taco shops, financial investment. In this context, don't confuse the word domain with internet things like the Domain Name System or domain name.

By scoping the sentiment measure to specific domains of thought, more accurate conclusions might be drawn. In other words, the sentiment is about related things, apples-to-apples, and not apples-to-oranges.

The targetted text will focus on subjective writings, rather than objective. For example, text like technical designs or API documentation will be avoided. Instead, text like news headlines, user posts, user reviews, op-eds, etc., will be included.

Since the Google Sentiment API is not infinitely free to call, various limits will be placed on the coverage of this application.

## Reinventing the Wheel?
Yes, efforts and apps like this have already been created. However, it's the process of designing and building it that's fun. The results might be interesting to interpret or present in different ways, but it's more about the dev journey.

## Functional
The sentiment results will be exposed by a readonly REST API (like, GET /pulse/{domain}). Currently, no plans for an exposed CRUD interface. To control cost and stay within free tiers on hosting and sentiment API calls, the API will be consumed only by a web page served by this app.

## Channel Configuration
Since there can be many domains over which sentiment will be measured, the details of each domain must be configured.

General idea for retaining domain configuration...

```
{
  "domains" : {
    "domain_x": {
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
    "domain_y": {
      // sources    
    }
}
```

## Sentiment Collection Process Overview
On a schedule (e.g., once per day), the fetch, measure, average, and save process will execute in the background. The results of the process will be available to the exposed readonly REST interface for serving up to clients.

For each configured domain:
1. Fetch the source(s)
2. For each source, extract the information to submit to the sentiment API
3. Average the sentiment results (measurements). Overall, this is the pulse of the specific domain.
4. Save the pulse to a persistent storage for the given domain; timestamped so that a time series can accrue.

Many details are not yet finalized. For example, in #2, exactly which information is extracted? If every source is just slightly different, even with same payload format, how to make the extraction *easily* extensible and apples-to-apples?

TODO:
* Does the sentiment API work best with short snippets or longer text?
* How are sentiment API charges incurred (call count, message size, etc.)?

## Pulse Read API
The pulse read API (e.g., GET /pulse/{domainId}) will return the pulse for one or more configured domains. Optional parameters to the root API will be introduced to affect the data returned.

`GET /api/1/pulse/` Returns the most recent sentiment data for all available domains.

Example payload:
```
{
  "data": {
    "kind": "pulse",
    "items": [{
        "kind": "pulse",
        "domainId": "v",
        "summary:" "This is the pulse of the domain...",        
        "measure": 7,
        "updated": "2018-01-02T23:11:44",
        "link": {
          "rel": "self",
          "href": "/api/1/pulse/auto.truck.review",
          "type": "GET"
        }
      },
      {
        "kind": "pulse",
        "domainId": "website.reddit.r.news",
        "summary:" "This is the pulse of the domain...",
        "measure": 7,
        "updated": "2018-01-02T25:11:54",
        "link": {
          "rel": "self",
          "href": "/api/1/pulse/website.reddit.r.news",
          "type": "GET"
        }
      }]
  }
```

`GET /api/1/pulse/website.reddit.r.news` Returns the most recent sentiment data for domain "domain_y".

Example payload:
```
{
  "kind": "pulse",
  "domainId": "website.reddit.r.news",
  "summary:" "This is the pulse of the domain...",
  "measure": 7,
  "updated": "2018-01-02T25:11:54",
  "link": {
    "rel": "self",
    "href": "/api/1/pulse/website.reddit.r.news",
    "type": "GET"
  }
}
```
`GET /pulse/?domain=domain_x|domain_w|domain_a` Returns the most recent sentiment data for domains x, w, and a. Order not guaranteed.


`GET /domain/` Returns a list of the available domains for which sentiment is calculated. The sentiment values are not returned.
```
{
  "data": {
    "kind": "domainMetaInfo",
    "items": [{
      "kind": "domainInfo",
      "domainId": "website.reddit.r.news",
      "summary": "This is the pulse of the domain...",
      "created": "2017-04-0326:13:00"
    },
    {
      "kind": "domainMetaInfo",
      "domainId": "auto.truck.review",
      "summary": "This is the pulse of the domain...",
      "created": "2017-10-0326:13:00"
    }]
  }
}
```







