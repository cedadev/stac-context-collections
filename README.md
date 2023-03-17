# STAC - Context Collections

- **Title:** Context Collections
- **OpenAPI specification:** [openapi.yaml](openapi.yaml)
- **Conformance Classes:**
  - <https://api.stacspec.org/v1.0.0-beta.3/item-search#context-collections>
- **Scope:** STAC - Core
- **[Extension Maturity Classification](https://github.com/radiantearth/stac-api-spec/tree/main/README.md#maturity-classification):** Proposal
- **Dependencies:**
  - [Item Search](https://github.com/radiantearth/stac-api-spec/tree/master/item-search)
  - [Context Extension](https://github.com/radiantearth/stac-api-spec/tree/master/fragments/context)
- **Owner**: @agstephens
  
This defines a new parameter, `collections` inside the context response which is a summary of all the collections which are part of the current result
context. i.e. All the collections that apply to the current search results. This is to be used in conjuntion with the [filter extension](https://github.com/radiantearth/stac-api-spec/tree/master/fragments/filter).
The `/queryables` endpoint provides the intersect of all the properties for all the collections. For endpoints wit few collections or shared properties, there may be no
issue, but where the number of different property sets increases, the intersection of these properties could be null. This renders the `/queryables` endpoint near useless.
See [issue 182](https://github.com/radiantearth/stac-api-spec/issues/182).

This extension is created to alleviate this problem by providing the client with the collection summary in context. This then allows the client to use the proposed
`/queryables?collection1,collection2` to build the intersect.

`/queryables` is assumed to be all so implementations of this extension can use no collections in the response to mean all, assuming that returned > 0.

Building the intersect is an expensive task so it is likely that caching would be important.

Depending on your database backend, you might be able to get a different level of detail back in this response. For elasticsearch, the default number returned in an aggregation is 10, so the `collections` response in the context would be the top 10 collections returned, by count (i.e. top  10 collections with the most items which match your search). This should be sufficient as the more collections you intersect overlapping queryables will tend to 0.

Your implementation my determine what is sufficient for your use case where including more will lead to a more expensive intersect query.

It is recommended to short-circuit the intersect process if the intersect reaches 0.

## HTTP GET

### Examples

```json
  "context": {
    "returned": 10,
    "limit": 10,
    "result_count": 174,
    "collections": [
      "Ic93XnsBhuk7QqVbSFwS"
    ]
  }
```
