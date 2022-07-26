# Auto

Automatic flow receives a text file (DOCX) and returns a ready-for-download archive with audiofiles inside (audiobook format).
If an error occurs, the Speechki editorial team will be notified automatically, fix the problem, and restart the process.


## States:
- conversion (The system works on text formatting)
- conv_in_progress (The text formatting process was interrupted by a technical issue which is being fixed by Speechki editorial team)
- record (The system records the text)
- record_in_progress (The recording process was interrupted by a technical issue which is being fixed by Speechki editorial team)
- build (The system put together audiofiles and makes post-production)
- build_in_progress (The post-production process was interrupted by a technical issue which is being fixed by Speechki editorial team)
- order_completed (The order is completed; Files are ready to be download)


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

You created order via API and our system started working on the uploaded text.

Actions that will be initiated after transition:

- Webhook notification
- Conversion process (text formatting)


The transition will be initiated automatically by Speechki


---


#### conversion -> record

The system completed the conversion process (text formatting) and started recording the text.


Actions that will be initiated after transition:

- Recording process


The transition will be initiated by the trigger


---


#### conversion -> conv_in_progress

The system completed the conversion process (text formatting) with an error. Speechki editorial team is working on fixing issues that appeared. Once fixed, the text will be put back into the flow.


The transition will be initiated by the trigger


---


#### conv_in_progress -> record

Speechki editorial team fixed all the issues and put back the text into the flow.


Actions that will be initiated after transition:

- Recording process


The transition will be initiated by the Speechki Editorial Team


---


#### record -> build

The system completed the recording process and started post-production process (building).


Actions that will be initiated after transition:

- Building process (post-production)


The transition will be initiated by the trigger


---


#### record -> record_in_progress

The system completed the recording process with an error. Speechki editorial team is working on fixing issues that appeared. Once fixed, the text will be put back into the flow.


The transition will be initiated by the trigger


---


#### record_in_progress -> build

Speechki editorial team fixed all the issues and put back the text into the flow.


Actions that will be initiated after transition:

- Building process (post-production)


The transition will be initiated by the Speechki Editorial Team


---


#### build -> order_completed

The system completed the post-production process (building) and closed the order.


Actions that will be initiated after transition:

- Webhook notification
- Turn the order into Completed status


The transition will be initiated by the trigger


---


#### build -> build_in_progress

The system completed the prost-production process (building) with an error. Speechki editorial team is working on fixing issues that appeared. Once fixed, the text will be put back into the flow.


The transition will be initiated by the trigger


---


#### build_in_progress -> order_completed

Speechki editorial team fixed all the issues and put back the text into the flow.


Actions that will be initiated after transition:

- Webhook notification
- Turn the order into Completed status

The transition will be initiated by the Speechki Editorial Team


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
