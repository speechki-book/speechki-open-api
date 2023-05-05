# Sample Outsource Workflow

This workflow streamlines the process of transforming text into ready-to-download audio files. The Speechki editorial team is responsible for proofreading and ensuring top-notch quality throughout the process. In the event of an error, the team will be notified automatically, fix the problem, and restart the process.

## Workflow States:
1. **conversion**: The system works on text formatting to optimize it for audio production.
2. **conv_in_progress**: The text formatting process is interrupted by a technical issue, which is being resolved by the Speechki editorial team.
3. **record**: The system records the formatted text.
4. **record_in_progress**: The recording process is interrupted by a technical issue, which is being resolved by the Speechki editorial team.
5. **voiced**: Audio editing is performed to enhance the quality of the recordings.
6. **internal_check**: The audio undergoes an internal review to ensure quality and consistency.
7. **build**: The system assembles the audio files and performs post-production tasks.
8. **build_in_progress**: The post-production process is interrupted by a technical issue, which is being resolved by the Speechki editorial team.
9. **order_completed**: The order is completed, and the audio files are ready for download.



## Transitions:


### Optimistic Way

- None -> conversion
- conversion -> record
- record -> voiced
- voiced -> internal_review
- internal_review -> build
- build -> order_completed


### Full flow

- None -> conversion
- conversion -> record
- conversion -> conv_in_progress
- conv_in_progress -> record
- record -> voiced
- record -> record_in_progress
- record_in_progress -> voiced
- voiced -> internal_review
- internal_review -> voiced
- internal_review -> build
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


#### record -> voiced


The system has successfully completed the record process. Now Speechki editors can listen audiobook and fix mistakes.


Actions which will be started after transition:

-


How makes transition - by trigger


---


#### record -> record_in_progress

The system completed the recording process with an error. Speechki editorial team is working on fixing issues that appeared. Once fixed, the text will be put back into the flow.


The transition will be initiated by the trigger


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


#### internal_check -> build


The author approve edits and send the order to build.


Actions which will be started after transition:

- 


How makes transition - human


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

- Customer flow - `1` (use "sample" flow)

Endpoint - [create](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v2.md#create-sample)

### Request body


```json
{
  "text": "Hello World!",
  "customer_flow": 1,
  "details": {
    "name": "Test Sample",
    "comment": "Some comment for us",
    "remote_key": "id-from-your-system",
    "speaker": 214,
    "speed": 1,
    "volume": 1
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
      "name": "Test Sample",
      "raw_authors": [
        {
          "first_name": "Default",
          "last_name": ""
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
      "book_language": "english"
    },
    "is_completed": false,
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
    "isbnx": null,
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
  "remote_key": "id-from-your-system",
  "order": {
    "id": "string",
    "details": {
      "name": "Test Sample",
      "raw_authors": [
        {
          "first_name": "Default",
          "last_name": ""
        }
      ],
      "comment": "Some comment for us",
      "remote_key": "id-from-your-system",
      "speaker": 214,
      "speed": 1,
      "volume": 1,
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
