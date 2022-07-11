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

- Record process


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
