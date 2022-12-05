# External reviewer

Speechki editors are responsible for proofreading. Author are responsible for checking the results.


Author has 3  rounds of edits. After that he has one option - accept edits and move the order to "build" state.


User have access to editor only when the order in "check" state. In this situation when you receive order details you can see field **guest_token_link** and this field is not null.


## Order Transition limit

  In [order detail endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v2.md#retrieve-order) you can see fields `approvals_count`, `max_approvals_count` and if `max_approvals_count` is not None `current_approval`.
  
  `approvals_count` - Integer. The number of transitions already made.
  `max_approvals_count` - Integer or None. The max number of transition.
  `current_approval` - Optional string. Current round.


## States:
- approved (Text preparation)
- ready_to_voice (Text review)
- record (Record in progress)
- record_in_progress (Record is broken and people are fixing it)
- voiced (Audio editing)
- internal_check (Internal review)
- check (The audiobook is on the author review)
- build (Build in progress)
- build_in_progress (Build is broken and people are fixing it)
- order_completed (The order is completed)


## Transitions:


### Optimistic Way

- None -> approved
- approved -> ready_to_voice
- ready_to_voice -> record
- record -> voiced
- voiced -> internal_check
- internal_check -> check
- check -> build
- build -> order_completed


### Full flow

- None -> approved
- approved -> ready_to_voice
- ready_to_voice -> approved
- ready_to_voice -> record
- record -> voiced
- record -> record_in_progress
- record_in_progress -> voiced
- voiced -> internal_check
- internal_check -> voiced
- internal_check -> check
- check -> internal_check
- check -> build
- build -> order_completed
- build -> build_in_progress
- build_in_progress -> order_completed


### Description


---

#### None -> approved


It is first transition. You created order via API and our system started work on a text.


Speechki editors prepare text to record.

Actions which will be started after transition:

- Webhook notification
- Conversion process


How makes transition - automatically after order creation


---


#### approved -> ready_to_voice

Speechki editor completed to prepare text and send to main editor on review.


Actions which will be started after transition:
-


How makes transition - human


---


#### ready_to_voice -> approved

The editor-in-chief left the edits and return the order to the editors.


Actions which will be started after transition:

-


How makes transition - human


---


#### ready_to_voice -> record


The editor-in-chief approve text and send the order to record.


Actions which will be started after transition:

- Webhook notification
- Recording process


How makes transition - human


---


#### record -> voiced


The system has successfully completed the record process. Now Speechki editors can listen audiobook and fix mistakes.


Actions which will be started after transition:

-


How makes transition - by trigger


---


#### record -> record_in_progress


The system completed the record process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### record_in_progress -> voiced

Editors fixed all problems. After that they are returning the order to stream. Now Speechki editors can listen audiobook and fix mistakes.


Actions which will be started after transition:

- 


How makes transition - human


---


#### voiced -> internal_check


The Editor send the order on The editor-in-chief review.


Actions which will be started after transition:

- 


How makes transition - human


---


#### internal_check -> voiced


The editor-in-chief left the edits and return the order to the editors.


Actions which will be started after transition:

-


How makes transition - human


---


#### internal_check -> check


The editor-in-chief approve edits and send the order to client review.


Actions which will be started after transition:

- Webhook notification


How makes transition - human


---


#### check -> internal_check


The author left the edits and return the order to the editors.


Actions which will be started after transition:

- Webhook notification


How makes transition - human


---


#### check -> build


The author approve edits and send the order to build.


Actions which will be started after transition:

- Webhook notification


How makes transition - human


---


#### build -> build_in_progress


The system completed the build process with an error, so our editors will fix problems and return book to stream.


How makes transition - by trigger


---


#### build -> order_completed

The system has successfully completed the record process and closed the order.


Actions which will be started after transition:

- Webhook notification
- Marks the order as completed


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
      "book_language": "english"
    },
    "is_completed": false,
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
    "type_productions": "outsource"
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


#### Message for Webhook after proofreading

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
      "book_language": "english"
    },
    "is_completed": true,
    "book_id": 1,
    "state": {
      "id": 7,
      "label": {
        "eng": "Service review"
      },
      "slug": "check"
    },
    "created": "2021-07-14T13:32:48.958Z",
    "modified": "2021-07-14T13:32:48.959Z",
    "isbnx": "111111111",
    "type_productions": "outsource"
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
  "builds": [],
  "other_builds": []
}
```


#### Data from detail order endpoint in "check" state

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
      "id": 7,
      "label": {
        "eng": "Service review"
      },
      "slug": "check"
    },
    "created": "2022-07-12T12:00:39.815Z",
    "modified": "2022-07-12T12:00:39.815Z",
    "type_productions": "outsource",
    "is_completed": false,
    "guest_token_link": "https://hermes.books.speechki.org/api/v2/orders/orders/string/link/"
  },
  "available_transitions": [
        {
            "action_text": {
                "eng": "Accept Proof"
            },
            "description": {
                "eng": "Are you sure you want to accept Proof?"
            },
            "destination_state": 14,
            "destination_state_obj": {
                "id": 14,
                "label": {
                    "eng": "Build in progress"
                },
                "slug": "build"
            },
            "conditions": [
                {
                    "is_accept": true,
                    "name": "No active corrections"
                }
            ],
            "is_accept": true,
            "link": "https://hermes.books.speechki.org/api/v2/orders/orders/string/change_state/8/",
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
      "book_language": "english"
    },
    "is_completed": true,
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
    "type_productions": "outsource"
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
