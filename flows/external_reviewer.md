# External reviewer

Speechki editors are responsible for proofreading. Author are responsible for checking the results.


Author has 3  rounds of edits. After that he has one option - accept edits and move the order to "build" state.


User have access to editor only when the order in "check" state. In this situation when you receive order details you can see field **guest_token_link** and this field is not null.


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