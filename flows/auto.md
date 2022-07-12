# Auto

It is flow where the system receives input book (docx file) and returns complete build. This flow doesn't need human help.
If an error occurs, the editors from Speechki will fix it.

If you use this flow, you can't edit text and audio. You can download complete audiobook.


## States:
- conversion (Conversion in progress)
- conv_in_progress (The Conversion is broken and people are fixing it)
- record (Record in progress)
- record_in_progress (Record is broken and people are fixing it)
- build (Build in progress)
- build_in_progress (Build is broken and people are fixing it)
- order_completed (The order is completed)


## Transitions:


### Optimistic Way

- None -> conversion
- conversion -> record
- record -> build
- build -> order_completed


### Full flow

- None -> conversion
- conversion -> record
- conversion -> conv_in_progress
- conv_in_progress -> record
- record -> build
- record -> record_in_progress
- record_in_progress -> build
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

- Record process


How makes transition - by trigger


---


#### conversion -> conv_in_progress

The system completed the conversion process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### conv_in_progress -> record

Editors fixed all problems. After that they are returning the order to stream.


Actions which will be started after transition:

- Recording process


How makes transition - human


---


#### record -> build

The system has successfully completed the record process and started build the audiobook.


Actions which will be started after transition:

- Build process


How makes transition - by trigger


---


#### record -> record_in_progress

The system completed the record process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### record_in_progress -> build

Editors fixed all problems. After that they are returning the order to stream.


Actions which will be started after transition:

- Build process


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

- Customer flow - `1` (use "auto" flow)

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
      "id": 6,
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
            "id": 6,
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


#### Message to Webhook after complete the order

```json
{
  "notification_id": 2,
  "event_type": "change_state",
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
    "book_id": null,
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
            "id": 6,
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