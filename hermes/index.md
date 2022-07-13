# Hermes

Hermes is a service providing integration with Speechki CRM to create orders (books) over REST API.

At the moment we don't have a sandbox, so all the integration tests will be made in production environment.


## Requirements

To work with the API you will need:
- a Customer in Speechki CRM;
- a Customer token to call Hermes REST API;
- an endpoint for WebHook which will be receiving requests from our side.


## Customer Token

Customer's admin and manager can create the token. The token has no lifetime limit. For a token to be revoked it must be destroyed.

Token helps us identify the current Customer and user. All of the actions that will be performed with the token will be written under the user owning the token.

HTTP Header — **Authorization**.

Authorization header format — **Token waGpB26t.JXD7RPEHqJVdTCy2AZl7HM0Garmv9DQB**


## Embedded

You can redirect a user to this URL or embed an iframe with it. Recommended minimum width for the iframe is 1200px.

`<iframe src="guest_link" />`

You can read about [*Guest Link*](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#guest-link) below. 

Additionally to params that guest link contains, you can also append `lang` parameter to change editor language. `...&lang=eng`.
Current available languages:
* `eng` - English

Embed Editor also provides methods to interact with the corresponding Speechki CRM order. In the top bar of the window there would be controls for changing order state (if any is available). 

When editing process is over and the order is closed iframe sends `postMessage` to its parent containing event object in following notation: 

`
  { event: 'speechki-close-editor', data: {} }
`

If there is no data related to the event, data field will be `null`.

For right now there is only one event which signals order closing. 

## Swagger Doc

Up-to-date documentation for the endpoints is available at https://hermes.books.speechki.org/docs.


## Docs

At the moment we have 2 versions API.

[V1](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v1.md) is stable but have fewer features.

[V2](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/v2.md) have more feature but not stable at the moment.


## Entities


### Book languages

This endpoint is absolutely open for requests.

It is a dictionary where key is a unique ID for the language and value is a label for this language.

These values are used for filtering voices (speakers) and creating the book (text parsing).

Book languages [endpoint](https://hermes.books.speechki.org/docs#/speech_settings.v1/get_book_languages_handler_api_v1_speech_settings_languages__get).


### Speakers

This endpoint requires an Auth Token.

List of voices you can use to create books. Your decision will affect the voice of the book.

This endpoint requires a query parameter [**book_language**](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#book-languages).

Speakers [endpoint](https://hermes.books.speechki.org/docs#/speech_settings.v1/get_speakers_handler_api_v1_speech_settings_speakers__get).


### Speakers by customer id


List of voices you can use to create books. Your decision will affect the voice of the book.

This endpoint requires:
- a customer id from Speechki system,
- a query parameter [**book_language**](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#book-languages).

Speakers by customer id [endpoint](https://hermes.books.speechki.org/docs#/speech_settings.v1/get_speakers_by_customer_handler_api_v1_speech_settings_speakers_by_customer__customer_id___get).


### Customer flows

Every customer has list of available flows, rows from this list can be used to more precisely control order creation.

In order creation it is field `customer_flow`.

This endpoint requires an Auth Token.

You can see [endpoint](https://hermes.books.speechki.org/docs#/customers.v2/customer_flows_handler_api_v2_customers_orders_customer_flows__get).


## flows

- [**auto**](https://github.com/speechki-book/speechki-open-api/blob/master/flows/auto.md)
- [**simple integration**](https://github.com/speechki-book/speechki-open-api/blob/master/flows/simple_integration.md)
- [**external reviewer**](https://github.com/speechki-book/speechki-open-api/blob/master/flows/external_reviewer.md)