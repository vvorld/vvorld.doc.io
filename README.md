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
    "application": {
        "documents": [
            {
                "id": "string",
                "issuingCountry": "JPN",
                "documentType": "id-card",
                "files": [
                    {
                        "id": "string",
                        "kind": "front",
                        "uri": "data:image/png;base64,R0lGODlhDAAMAK..."
                    }
                ]
            }
        ]
    },
    "verificationTypes": [
        "data-extraction"
    ]
}
```

**To request PEPs:**

```json
{
    "application": {
        "fields": [
            {
                "contentType": "string",
                "content": "First Name",
                "category": "Artem"
            },
            {
                "contentType": "string",
                "content": "Last Name",
                "category": "Gerus"
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
        ]
    },
    "verificationTypes": [
        "watchlists"
    ]
}
```

### Curl example

There is [example to post application to verification using curl](Curl_example.txt). This example contains images of both sides of Estonian id card.

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

**Example:**

```json
{
  "id": "5e62110968e3fb4d655756d1",
  "metadata": {
    "externalId": "ID-2345",
    "platform": "android",
    "clientName": "my-api-client",
    "clientVersion": "1.0.0",
    "labels": {
      "my-custom-meta-name-1": "custom-value-1",
      "my-custom-meta-name-2": "custom-value-2"
    }
  },
  "application": {
    "fields": [
      {
        "contentType": "string",
        "content": "First Name",
        "category": "Artem",
        "requireCheck": true
      },
      {
        "contentType": "string",
        "content": "Last Name",
        "category": "Gerus",
        "requireCheck": true
      },
      {
        "contentType": "sex",
        "content": "male",
        "category": "sex"
      },
      {
        "contentType": "bool",
        "content": "true",
        "category": "Agree on something"
      }
    ],
    "documents": [
      {
        "id": "string",
        "issuingCountry": "JPN",
        "documentType": "id-card",
        "files": [
          {
            "id": "string",
            "kind": "front",
            "uri": "data:image/png;base64,R0lGODlhDAAMAK..."
          }
        ]
      }
    ],
    "selfie": {
      "files": [
        {
          "id": "string",
          "uri": "data:video/webm;base64,<data>"
        }
      ]
    }
  },
  "verificationTypes": [
    "watchlists",
    "face-matching",
    "data-extraction"
  ],
  "processingState": "done",
  "overallResult": {
    "status": "declined",
    "comments": [
      {
        "service": "face-matching",
        "status": "approved",
        "comment": "faces are similar"
      },
      {
        "service": "watchlists",
        "status": "declined",
        "comment": "found 2 reports against the person"
      },
      {
        "service": "data-extraction",
        "status": "approved",
        "comment": "data was extracted"
      }
    ],
    "validationDate": "2020-03-17T20:28:37.061Z"
  },
  "servicesResults": {
    "faceMatching": {
      "comment": "Faces are not similar",
      "verifier": "Facemelter",
      "status": "declined",
      "processingState": "done",
      "results": [
        {
          "files": [
            "face-abc1",
            "document-photo-qwe7"
          ],
          "score": 0.3
        }
      ],
      "settings": {
        "declineThreshold": 0.4,
        "needsReviewThreshold": 0.6
      }
    },
    "dataExtraction": {
      "comment": "Data was extracted",
      "verifier": "Regula",
      "status": "approved",
      "extracted": [
        {
          "documentId": "passport-1",
          "ocr": [
            {
              "contentType": "string",
              "category": "First name",
              "content": "Joe"
            },
            {
              "contentType": "string",
              "category": "First name",
              "content": "Джо",
              "locale": "ru_RU"
            },
            {
              "contentType": "string",
              "category": "Last name",
              "content": "Black"
            }
          ],
          "mrz": [
            {
              "contentType": "string",
              "category": "First name",
              "content": "James"
            },
            {
              "contentType": "string",
              "category": "Last name",
              "content": "Black"
            }
          ],
          "nfc": [
            {
              "contentType": "string",
              "category": "First name",
              "content": "Joe"
            },
            {
              "contentType": "string",
              "category": "Last name",
              "content": "Black"
            }
          ]
        },
        {
          "documentId": "driving-licence-1",
          "ocr": [
            {
              "contentType": "string",
              "category": "First name",
              "content": "Joe"
            },
            {
              "contentType": "string",
              "category": "Address",
              "content": "May street"
            }
          ]
        }
      ]
    },
    "crossChecking": {
      "comment": "string",
      "verifier": "string",
      "status": "declined",
      "processingState": "done",
      "fieldChecking": [
        {
          "category": "First name",
          "field": "John",
          "extracted": "Joanna",
          "equal": false,
          "documentId": "passport-1"
        },
        {
          "category": "Surname",
          "field": "Doe",
          "extracted": "",
          "equal": false,
          "documentId": "passport-1"
        },
        {
          "category": "Date of Birth",
          "field": "1999-09-31",
          "extracted": "1999-09-31",
          "equal": true,
          "documentId": "passport-1"
        },
        {
          "category": "Address",
          "field": "May street",
          "extracted": "April street",
          "equal": false,
          "documentId": "driving-licence-1"
        }
      ]
    },
    "watchlists": {
      "comment": "string",
      "verifier": "string",
      "status": "declined",
      "processingState": "done",
      "sentFields": [
        "First name",
        "Last name",
        "Date of Birth"
      ],
      "records": [
        {}
      ]
    }
  },
  "responseCode": 200
}
```

### Reading results

The complete description of application format is [Enveloped Application](enveloped-application.yaml).

## Admin Panel

To review results of verification services you should use the Admin Panel.

By default Admin Panel address is the same as the address of the sandbox provided by GetID.