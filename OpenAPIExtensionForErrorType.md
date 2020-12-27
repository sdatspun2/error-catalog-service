# OpenAPI Extension for Error Type

[OpenAPI](http://spec.openapis.org/oas/v3.0.3) specification (OAS) does not provide any schema element(s) (fields, objects) to list errors that various operations of the service can emit. However, OAS allows to define [extensions](http://spec.openapis.org/oas/v3.0.3#specification-extensions). To relate error types used by an API to those defined in [Error Catalog Service](README.md), we have defined an extension.

Using an [OpenAPI Document](http://spec.openapis.org/oas/v3.0.3#openapi-document) (API definition) annotated with this extension, various tools or software systems including but not limited to API portal, service implementing the API definition, CI/CD utilities and QA tests can retrieve error types from the Error Catalog Service.


### `x-error-types`

This extension lists an array of URIs of error types that correspond to the errors emitted by operations annotated using this extension.

```
{
      "type": "array",
      "description": "An array of error types, each identified using a URI.",
      "items": {
        "type": "string",
        "format": "uri",
        "description": "A URI reference that identifies an error type in an error catalog."
      }
}
```

#### Example

The following example of API definition relates error types to an API operation identified as `post-card-payment`. This relationship is annotated using the extension `x-error-types`. Error types are defined in error catalog identified as `generic` for generic error types and in error catalog identied as `payment` for payment method specific errors.

```
  "paths": {
    "/v1/payment/card-payments": {
      "post": {
        "summary": "Post card payment",
        "tags": [
          "post-card-payment"
        ],
        "operationId": "post-card-payment",
        "x-error-types": [
          "/v1/error/error-catalogs/payment/error-types/out-of-credit",
          "/v1/error/error-catalogs/payment/error-types/card-type-invalid",
          "/v1/error/error-catalogs/payment/error-types/brand-invalid",
          "/v1/error/error-catalogs/payment/error-types/last4-invalid",
          "/v1/error/error-catalogs/payment/error-types/expiry-invalid",
          "/v1/error/error-catalogs/generic/error-types/resource-not-found",
       ],
        "responses": {
          "201": {
            "description": "OK",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/card-payment"
                }
              }
            }
          },
          "default": {
            "description": "unexpected error",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "./models/error.json"
                }
              }
            }
          }
        },
        "description": "Post card payment"
      },
      "parameters": []
    }
```

##### Error Response

For the `POST` operation, `post-card-payment`, the error response could be as following.

```
POST https://example.com/v1/payment/card-payments
...
HTTP/1.1 403 Forbidden
Content-Type: application/json
Content-Language: en
  {
     "type": "https://example.com/v1/error/error-catalogs/payment/error-types/out-of-credit",
     "title": "You do not have enough credit.",
     "detail": "Your current balance is 30, but that costs 50.",
     "id": "b60136e4-da06-4928-a4b2-5797b790e685",
     "instances": [
       {
	       "keyword_location": "/account",
	       "instance_location":"/account/12345",
	       "in":"body",
	       "detail":"Your current balance is 30, but that costs 50.",
       }
     ],
     "links": [
       {
	       "href": "https://example.com/resolutions/out-of-credit",
	       "rel": "resolution",
       }
     ]
  }
```

### Use Cases

1. API portals can show a list of error types associated with an API using the Error Catalog Service.
2. Linters can validate if an API definition annotated with this extension relates to correct error types in the Error Catalog Service.
3. API automation tests can validate if the error responses emitted by the service implementing the API are as documented in the API definition.

### Example API definition

The [API definition](error-catalog-service.json) for the Error Catalog Service itself is annotated with `x-error-types` extension.

**Note**: Since this is just an API (no implementation), The API definition uses a fictitious variable called `{ecid}` for identifying the error catalogs.

## Community

* Contribute via issues and pull requests.
* Contributors: people and organizations who helped us get started or are actively working on the definition of Error Catalog Service API, various extensions and implementation of the service in various languages.
* Demos & open source -- if you have something to share about your use of Error Catalog Service, please submit a PR!
