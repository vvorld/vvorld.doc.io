# GetID Sandbox Customer Documentation
The purpose of this page is to provide guide how to work with GetID sandbox.

## Basics

Sandbox is a test environment deployed on GetID servers to familiarize customer's developers with verification services provided by GetID.

You are able to interact with sandbox using our SDKs (iOS, Web and Android) or REST API. Admin Panel is avalable as well as a part of sandbox.

## Requirements

First of all you should have an address of the sandbox and API key provided by GetID.

## Data Capture SDK

There are links to SDK Documentation and example-projects.

- [Web](https://github.com/vvorld/getid-web-sdk)
- [iOS](https://github.com/vvorld/getid-ios-sdk)
- [Android](https://github.com/vvorld/getid-android-sdk)

## REST API

There is [API specification](https://vvorld.github.io/) in [openAPI standard](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md).

### Application example

There are example with minimal data passed to GetID to complete verification:

**To extract data from photo of a document:**

```json
{
    "envelopedApplication": {
       "extensions": [],
       "application": {
           "fields": [],
           "documents": [
               {
                 "issuingCountry": "ee",
                 "documentType": "passport",
                 "images": [
                   {
                     "name": "front",
                     "size": "45932",
                     "format": "base64",
                     "kind": "front",
                     "mediaType": "image/jpeg",
                     "blob": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAA"
                   }
                 ]
               }
             ]
       }
   }
}
```

**To request PEPs:**

```json
{
    "envelopedApplication": {
       "extensions": [],
       "application": {
           "fields": [
               {
                 "contentType": "string",
                 "category": "First name",
                 "content": "fname"
               },
               {
                 "contentType": "string",
                 "category": "Last name",
                 "content": "lname"
               },
               {
                 "contentType": "string",
                 "category": "Country",
                 "content": "Estonia"
               },
               {
                 "contentType": "string",
                 "category": "Postcode",
                 "content": "11456"
               },
               {
                "contentType": "string",
                "category": "Date Of Birth",
                "content": "1991-02-03"
                }
           ],
           "documents": []
       }
   }
}
```

### Curl example

There is [example to post application to verification using curl](POST_example.txt). This example contains images of both sides of Estonian id card.

**NB!** Change before execute:
- `<getidserver>` with address of the sandbox provided by GetID.
- `1231223` with the authorization bearer provided by GetID.

### Getting result

You are able to get the verification result in two ways:
- by callback;
- by API;

To get the result by callback you must specify callback URL in Admin Panel (Settings->Callback URL). 

Also you are able to request result using API method `/plugins/v1/acceptor/application/{appId}`.

The result is in [Enveloped Application format](enveloped-application.yaml).

More details in public available in [public API specification](public-api.yaml). 

**Example:**

```json
{
    "envelopedApplication": {
        "extensions": [],
        "application": {
            "fields": [
                {
                    "contentType": "string",
                    "category": "First name",
                    "content": "fname"
                },
                {
                    "contentType": "string",
                    "category": "Last name",
                    "content": "lname"
                },
                {
                    "contentType": "string",
                    "category": "Date Of Birth",
                    "content": "1991-08-15"
                },
                {
                    "contentType": "string",
                    "category": "rdt_First name",
                    "content": "fname;anotherVariant"
                },
                {
                    "contentType": "string",
                    "category": "rtd_Date Of Birth",
                    "content": "1991-08-15;2001-08-15"
                }
            ],
            "documents": [
                {
                    "issuingCountry": "ee",
                    "documentType": "passport",
                    "images": [
                        {
                            "name": "front",
                            "size": "45932",
                            "format": "base64",
                            "kind": "front",
                            "mediaType": "image/jpeg",
                            "url": "https://some.address.to.dounload.image"
                        }
                    ],
                    "extractedData": [
                        {
                            "contentType": "string",
                            "category": "ext_First name",
                            "content": "anotherVariant"
                        },
                        {
                            "contentType": "string",
                            "category": "ext_Date Of Birth",
                            "content": "2001-08-15"
                        }
                    ]
                }
            ]
        }
    }
}
```

### Reading results

The complete description of application format is [Enveloped Application](enveloped-application.yaml).

All extracted data from documents will be written into the extractedData array. All of the extracted fields will get a prefix `ext_` in it’s category value which stands for `extracted`.

```json
{
"contentType": "string",
"category": "ext_First name",
"content": "John"
},
```

Extracted value will be placed under `content`.
If provided data doesn’t match the extracted data then additional fields with category prefix `rtd_` and category will be added to the fields array. In this case `content` contains both values provided and extracted respectively.

```json
{
    "contentType": "string",
    "category": "rdt_First name",
    "content": "fname;John"
},
```

## Admin Panel

To review results of verification services you should use the Admin Panel.

By default Admin Panel address is the same as the address of the sandbox provided by GetID.