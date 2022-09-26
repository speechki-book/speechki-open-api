# Simple interation

In this flow author from customer's system can proofreading result of record and fix mistakes.


Access to the editor is through [url from API or you can embed an iframe with url from API.](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#embedded)


User have access to editor only when the order in "voiced" state. In this situation when you receive order details you can see field **guest_token_link** and this field is not null.


## States:
- conversion (Conversion in progress)
- conv_in_progress (The Conversion is broken and people are fixing it)
- record (Record in progress)
- record_in_progress (Record is broken and people are fixing it)
- voiced (Audio editing)
- build (Build in progress)
- build_in_progress (Build is broken and people are fixing it)
- order_completed (The order is completed)


## Transitions:


### Optimistic Way

- None -> conversion
- conversion -> record
- record - voiced
- voiced -> build
- build -> order_completed


### Full flow

- None -> conversion
- conversion -> record
- conversion -> conv_in_progress
- conv_in_progress -> record
- record -> voiced
- record -> record_in_progress
- record_in_progress -> voiced
- voiced -> build
- build -> order_completed
- build -> build_in_progress
- build_in_progress -> order_completed


### Description


---

#### None -> conversion

It is first transition. You created order via API and our system started work on a text.

Actions which will be started after transition:

- Webhook notification
- Conversion process


How makes transition - automatically after order creation


---


#### conversion -> record

The system has successfully completed the conversion process and started record the book.


Actions which will be started after transition:

- Recording process


How makes transition - by trigger


---


#### conversion -> conv_in_progress

The system completed the conversion process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### conv_in_progress -> record

Editors fixed all problems. After that they are returning the order to stream.


Actions which will be started after transition:

- Record process


How makes transition - human


---


#### record -> voiced


The system has successfully completed the record process. Now author can listen audiobook and fix mistakes.


Actions which will be started after transition:

- Webhook notification


How makes transition - by trigger


---


#### record -> record_in_progress

The system completed the record process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### record_in_progress -> voiced

Editors fixed all problems. After that they are returning the order to stream. Now author can listen audiobook and fix mistakes.


Actions which will be started after transition:

- Webhook notification


How makes transition - human


---


#### voiced -> build

Author complete proofreading and want to build the audiobook.


Actions which will be started after transition:

- Build process
- Webhook notification


How makes transition - human


---


#### build -> order_completed

The system has successfully completed the record process and closed the order.


Actions which will be started after transition:

- Webhook notification
- Marks the order as completed


How makes transition - by trigger


---


#### build -> build_in_progress

The system completed the build process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### build_in_progress -> order_completed


Editors fixed all problems. After that they are returning the order to stream.


Actions which will be started after transition:

- Webhook notification
- Marks the order as completed


How makes transition - human


## Example

Input params:

- Customer flow - `1` (use "simple_integration" flow)

Endpoint - [create](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v2.md#create-order)

### Request body


```json
{
  "url": "https://source.com/test_file.docx",
  "customer_flow": 1,
  "details": {
    "name": "Test Book",
    "raw_authors": [
      {
        "first_name": "Frank",
        "last_name": "Herbert"
      }
    ],
    "comment": "Some comment for us",
    "remote_key": "id-from-your-system",
    "speaker": 214,
    "speed": 1,
    "volume": 1,
    "isbnx": "111111111",
    "book_language": "english",
    "style_sheet_link": "https://source.com/style_sheet.json",
    "preview": "https://source.com/preview.jpg"
  }
}
```


### Message to Webhook after creation


```json
{
  "notification_id": 1,
  "event_type": "create",
  "remote_key": "id-from-your-system",
  "order": {
    "id": "string",
    "details": {
      "name": "Test Book",
      "raw_authors": [
        {
          "first_name": "Frank",
          "last_name": "Herbert"
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
      "isbnx": "111111111",
      "book_language": "english",
      "is_completed": false
    },
    "book_id": null,
    "state": {
      "id": 16,
      "label": {
        "eng": "Conversion in progress"
      },
      "slug": "conversion"
    },
    "created": "2021-07-14T13:32:48.958Z",
    "modified": "2021-07-14T13:32:48.959Z",
    "isbnx": "111111111",
    "type_productions": "client"
  },
  "transitions": [
    {
      "id": 1,
      "created": "2021-07-14T13:32:48.958Z",
      "source_state": null,
      "destination_state": {
            "id": 16,
            "label": {
                "eng": "Conversion in progress"
            },
            "slug": "conversion"
        }
    }
  ],
  "builds": [],
  "other_builds": []
}
```


#### Message for Webhook after book voiceover process

```json
{
  "notification_id": 2,
  "event_type": "change_state",
  "remote_key": "id-from-your-system",
  "order": {
    "id": "string",
    "details": {
      "name": "Test Book",
      "raw_authors": [
        {
          "first_name": "Frank",
          "last_name": "Herbert"
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
      "isbnx": "111111111",
      "book_language": "english",
      "is_completed": false
    },
    "book_id": 1,
    "state": {
      "id": 6,
      "label": {
        "eng": "Audio editing"
      },
      "slug": "voiced"
    },
    "created": "2021-07-14T13:32:48.958Z",
    "modified": "2021-07-14T13:32:48.959Z",
    "isbnx": "111111111",
    "type_productions": "client"
  },
  "transitions": [
    {
      "id": 1,
      "created": "2021-07-14T13:32:48.958Z",
      "source_state": null,
      "destination_state": {
            "id": 16,
            "label": {
                "eng": "Conversion in progress"
            },
            "slug": "conversion"
        }
    },
    ...
  ],
  "builds": [],
  "other_builds": []
}
```


#### Data from detail order endpoint in "voiced" state

Endpoint - [retrieve](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v2.md#retrieve-order)


```json
{
  "order": {
    "id": "string",
    "details": {
      "name": "Test Book",
      "raw_authors": [
        {
          "first_name": "Frank",
          "last_name": "Herbert"
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
      "isbnx": "111111111",
      "book_language": "english"
    },
    "book_id": 1,
    "isbnx": "111111111",
    "state": {
      "id": 6,
      "label": {
        "eng": "Audio editing"
      },
      "slug": "voiced"
    },
    "created": "2022-07-12T12:00:39.815Z",
    "modified": "2022-07-12T12:00:39.815Z",
    "type_productions": "client",
    "is_completed": false,
    "guest_token_link": "https://hermes.books.speechki.org/api/v2/orders/orders/string/link/"
  },
  "available_transitions": [
        {
            "action_text": {
                "eng": "Finish editing"
            },
            "description": {
                "eng": "Are you sure you want to complete the book?"
            },
            "destination_state": 10,
            "destination_state_obj": {
                "id": 10,
                "label": {
                    "eng": "Completed"
                },
                "slug": "order_completed"
            },
            "conditions": [],
            "is_accept": true,
            "link": "https://hermes.books.speechki.org/api/v1/orders/orders/string/change_state/10/",
            "approvals_count": 0,
            "max_approvals_count": null
        }
    ],
  "builds": [],
  "other_builds": []
}
```

#### Message to Webhook after complete the order

```json
{
  "notification_id": 3,
  "event_type": "change_state",
  "remote_key": "id-from-your-system",
  "order": {
    "id": "string",
    "details": {
      "name": "Test Book",
      "raw_authors": [
        {
          "first_name": "Frank",
          "last_name": "Herbert"
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
      "isbnx": "111111111",
      "book_language": "english",
      "is_completed": true
    },
    "book_id": 1,
    "state": {
      "id": 10,
      "label": {
        "eng": "Completed"
      },
      "slug": "order_completed"
    },
    "created": "2021-07-14T13:32:48.958Z",
    "modified": "2021-07-14T13:32:48.959Z",
    "isbnx": "111111111",
    "type_productions": "client"
  },
  "transitions": [
    {
      "id": 1,
      "created": "2021-07-14T13:32:48.958Z",
      "source_state": null,
      "destination_state": {
            "id": 16,
            "label": {
                "eng": "Conversion in progress"
            },
            "slug": "conversion"
        }
    },
    ...
  ],
  "builds": [
    {
      "bit_rate": 192,
      "duration": 100,
      "modified": "2021-07-14T13:32:48.959Z",
      "link": "https://host.com/some_book.wav",
      "build_type": "raw"
    },
    {
      "bit_rate": 192,
      "duration": 100,
      "modified": "2021-07-14T13:32:48.959Z",
      "link": "https://host.com/some_book_archive.zip",
      "build_type": "mp3_archive"
    }
  ],
  "other_builds": []
}
```
