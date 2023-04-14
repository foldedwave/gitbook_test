# Gateway Integration

## About This Guide

This guide provides the information required to integrate with our
Payment Gateway and gives a very basic example of code for doing so It
is expected that you have some experience in server-side scripting with
languages such as PHP or ASP; or that an off-the-shelf software package
is being used that has inbuilt or plug-in support for our Gateway.

## Terminology

The following terms are used throughout this guide:

**Gateway**

> The Payment Gateway.

**Merchant**

> The Merchant using the Gateway’s services.

**Our**

> The Payment Gateway Provider.

**You/your**

> The Merchant or its representative performing the integration.

**Acquirer**

> The bank or financial institution used by the Merchant.

**Customer**

> A Customer of the Merchant making a payment.

**Card**

> A payment credit, debit, prepayment or gift card issued by the Card
> Schemes.

**Card Scheme**

> The operator of a payment Card network, such as Visa, Mastercard, et
> al.

**Cardholder**

> The person who owns the payment Card, usually the Customer.

**Issuer**

> The bank or financial institution that issued the payment Card to the
> Cardholder.

**Merchant Account**

> An account on the Gateway mapped to an Acquirer-provided account.

**Checkout**

> Third-party checkout solution such as PayPal, Amazon Pay other
> alternative payment methods.

**Wallet**

> Third-party wallet solution such as Masterpass.

**Hosted Payment Page (HPP)**

> A page hosted on our secure server used to collect Customer details.

**Hosted Payment Field (HPF)**

> An individual form field hosted on our secure server used to collect
> sensitive Cardholder data.

## Integration Methods

There are three methods of integration provided to process your
transactions through the Gateway, allowing for different levels of
control and communication from your website.

### Hosted Integration

The Hosted Integration method makes it easy to add secure payment
processing to your ecommerce business, using our Hosted Payment Pages
(HPP). You can use this method if you do not want to collect and store
Cardholder data.

The Hosted Integration method works by redirecting the Customer to our
Gateway’s Hosted Payment Page, which will collect the Customer’s payment
details and process the payment before redirecting the Customer back to
a page on your website, letting you know the payment outcome. This
allows you the quickest path to integrating with the Gateway.

The standard Hosted Payment Page is designed to be shown in a lightbox
over your website and styled with logos and colours to match.
Alternatively, you can arrange for fully customised Hosted Payment Pages
to be produced that can match your website’s style and layout. These
fully customised pages are usually provided using a browser redirect,
displaying full-page in the browser, or can be displayed embedded in an
iframe on your website.

For greater control over the customisation of the payment page, our
Gateway offers the use of Hosted Payment Fields, where only the
individual input fields collecting the sensitive Cardholder data are
hosted by the Gateway while the remainder of the payment form is
provided by your website. These Hosted Payment Fields fit seamlessly
into your payment page and can be styled to match your payment fields.
When your payment form is submitted to your server, the Gateway will
submit a payment token representing the sensitive card data it collected
and your webserver can then use the Direct Integration to process the
payment without ever being in contact with the collected Cardholder
data. For more information, please refer to our Hosted Payment Fields
SDK Guide.

### Direct Integration

The Direct Integration works by allowing you to keep the Customer on
your system throughout the checkout process, collecting the Customer’s
payment details on your own secure server before sending the collected
data to our Gateway for processing. This allows you to provide a
smoother, more complete checkout process to the Customer.

In addition to basic sales processing, the Direct Integration can be
used to perform other actions such as refunds and cancellations, which
can provide a more advanced integration with our Gateway.

### Batch Integration

The Batch Integration is an enhancement to the Direct Integration,
allowing you to send multiple transactions in a single request and
monitor their status. This is useful if you wish to capture multiple
transactions or collect multiple payments – for example, collecting
subscription charges or loan repayments.

In addition to basic sales processing, the Batch Integration can be used
to perform other actions, such as refunds and cancellations, which can
provide a more advanced integration with our Gateway.

Unlike the Hosted and Direct Integrations, the Batch Integration does
not process transactions sent to it immediately. Instead, the Gateway
queues these transactions to be processed and returns a batch reference
number which can be used to download a file that contains the current
status of the transactions.

Batch Processing does not support transactions that require Customer
interaction such as 3-D Secure transactions, or alternative payment
methods with interactive Wallet or Checkout pages.

## Integration Libraries

We can provide a range of libraries to help you to integrate with the
Gateway.

These libraries include simple server-side classes in many popular
programming languages, through to client-side scripts to help with the
integration of the Hosted Payment Page or Hosted Payment Fields.

For more information about these libraries, please refer to appendix
A-1.

## Security and Compliance

Each method requires a different level of server security and compliance
with the Payment Card Industry Data Security Standard (PCI DSS).

If you use Hosted Payment Pages with the Hosted Integration or Hosted
Payment Fields with the Direct or Batch Integrations, then your
webserver does not need an SSL certificate and you require the lowest
level of PCI DSS compliance.

If your website collects and/or stores sensitive Cardholder data, such
as the card number (PAN) or card security code (CVV/CV2), then your
webserver must have an SSL certificate and serve all payment forms using
HTTPS. You will also need a higher level of PCI DSS compliance and to
complete a PCI validation form annually.

For more information, please see https://www.pcisecuritystandards.org/

##  Integration Details

### HTTP Requests

A request can be sent to the Gateway by submitting a HTTP POST request
to the integration URL provided.

The request should have a Content-Type:
application/x-www-form-urlencoded HTTP header and the request should be
name, value pairs URL encoded as per RFC 1738.

Example URL encoding:

merchantID=100001&action=SALE&type=1&amount=1001&currencyCode=826&countryCode=826&transactionUnique=55f6db1c81d95&orderRef=Test+purchase&customerPostCode=NN17+8YG&responseCode=0&responseMessage=AUTHCODE%3A350333&state=captured&xref=15091702MG47WN32MM88LPK&cardNumber=4929+4212+3460+0821&cardExpiryDate=1215

***Please note that the field names are cAsE sEnSiTiVe**.*

The response will use the same URL encoding and return the request
fields in addition to any dedicated response field. If the request
contains a field that is also intended as a response field, then any
incoming request value will be overwritten by the correct response
value.

### Hosted HTTP Requests

When using the Hosted Integration, the request must be sent from the
Customer’s web browser as the response will be a HTML Hosted Payment
Page (HPP), used to collect the Customer’s details. The format of the
request is designed so that it can be sent using a standard HTML form
with the data in hidden form fields. The browser will then automatically
encode the request correctly according to
application/x-www-form-urlencoded format.

When the Hosted Payment Page has been completed and the payment
processed, the Customer’s browser will be automatically redirected to
the URL provided via the **redirectURL** field. The response will be
returned to this page in application/x-www-form-urlencoded format, using
a HTTP POST request.

If the request contains a field that is also intended as a response
field, then any incoming request value will be overwritten by the
correct response value.

An example of a Hosted Integration request is provided in appendix
A-21.1 and sample code is provided in appendix A-22.1.

### Direct HTTP Requests

When using the Direct Integration, the response will be received in the
same URL encoded format, unless a **redirectURL** field is provided.

If a **redirectURL** field is provided, then the response will be a HTML
page designed to redirect a browser to the URL provided, using a HTTP
POST request containing the response. This allows you to collect the
Cardholder’s payment details on your own server, using a HTML form which
POSTs to the Direct Integration, which then effectively POSTs the
results back to this URL your webserver, where you can display the
transaction outcome.

If the request contains a field that is also intended as a response
field, then any incoming request value will be overwritten by the
correct response value.

An example of a Direct Integration request is provided in appendix
A-21.1 and sample code is provided in appendix A-22.1.

### Batch HTTP Requests

When using the Batch Integration, a single HTTP POST request can contain
multiple individual requests using the multipart/mixed content type with
a boundary string specified. Within that main HTTP request, each of the
parts contains a nested Direct Integration HTTP request, separated by
the boundary string.

Each part should begin with a Content-Type:
application/x-www-form-urlencoded HTTP header and contain a single
Direct Integration HTTP request, as documented in section 1.5.3.

You can optionally specify a Content-Id HTTP header to identify each
part message uniquely; if not provided, the Gateway will assign a unique
id to each part. The Content-Id HTTP header is returned in the response.
The Gateway will not validate the uniqueness of any id provided. After
the mandatory Content-type and the optional *Content-Id* header, two
carriage return/line feed pairs must follow (i.e. *\r\n\r\n*). Any
deviation from this structure might lead to the part being rejected or
incorrectly interpreted. The part request payload, formatted as a
regular HTTP URL encoded request, must follow the two-line breaks
directly.

To reduce the size of large batch requests, the Gateway supports
compression using a Content-Encoding HTTP header with either a ‘gzip’ or
‘x-gzip’ value. This header can be provided in the main request or in
the part request or both.

An Authorization HTTP header can be used in the request to provide the
username and password of a Gateway Merchant Management System user
account. If correct, the batch details will be recorded as having been
submitted by that user; if invalid, then the request will fail and
respond with a 401 (Unauthorised) HTTP status code.

The Gateway will respond in the same manner as the request with a
multipart/mixed content type; each part is the response to one of the
requests in the batched request. In addition, the response will contain
a standard Location HTTP header, providing a URL from which further
batch update responses can be downloaded; and a standard
Content-Disposition header, allowing a browser to download the response
to a file. If the request contained an Authorization HTTP header, then
the response will contain an X-P3-Token HTTP header containing an
authentication token that can be sent in future requests instead of the
username and password. The authentication token has a limited life span,
but each future request will return a new token and thus effectively
rejuvenate the token’s life.

Like the parts in the request, each response part contains a HTTP
response, including headers and body. Each response part is preceded by
a Content-Type HTTP header and Content-ID HTTP header. In addition, an
X-Transaction-ID HTTP header is added containing the requests
transaction id together with an X-Transaction-Response HTTP header
containing a textual description of the transaction processing status.

*  
*

*The Gateway will not process the transactions immediately but will
queue them up to process over time. The transactions may not be
processed in the order provided, so should not have interdependencies.
Transactions will only appear in the Merchant Management System when
they have been processed. The status of queued transaction is only
available by querying the status of the batch.*

The current status of a batch can be queried at any time by issuing a
HTTP GET request to the URL provided in the initial responses Location
HTTP header.

An Authorization HTTP header must be provided in the status request,
containing either the username and password of a Gateway Merchant
Management System user account or an authentication token returned in
the batch submission response’s X-P3-Token HTTP header. If a valid
username and password or a valid token is provided, then the response
will be an updated version of the initial submission response providing
the current status of each transaction. The response will only contain
transactions that the authenticated user has permission to view.

An example of a Batch Integration request is provided in appendix A-21.3
and sample code is provided in appendix A-22.3.1.

### Handling Errors

When the Gateway is uncontactable due to a communications error, or
problem with the internet connection, you may receive a HTTP status code
in the 500 to 599 range. In this situation, you may want to retry the
transaction. If you do choose to retry a transaction, then we recommend
that you perform a limited number of attempts with an increasing delay
between each attempt.

If the Gateway is unavailable during a scheduled maintenance period, you
will receive a HTTP status code of 503 ‘Service Temporarily
Unavailable’. In this situation, you should retry the transaction after
the scheduled maintenance period has expired. You will be notified of
the times and durations of any such scheduled maintenance periods in
advance, by email, and given a time when transactions can be
reattempted.

If you are experiencing these errors, then we recommend you consider the
following steps as appropriate for the integration method being used:

- Ensure the request is being sent to HTTPS and not HTTP. HTTP is not
  supported and is not redirected.

- Send transactions sequentially rather than concurrently.

- Configure your integration code with try/catch loops around individual
  transactions to determine whether they were successful or not and
  retry if required, based on the return code or HTTP status returned.

- Configure the integration so that if one transaction fails, the entire
  batch does not stop at that point – i.e. log the failure to be checked
  and then skip to the next transaction rather than stopping entirely.

### Redirect URL

The **redirectURL** request field is used to provide the URL of a
webpage on your server.

When provided, the Gateway will respond with a HTML page designed to
redirect the Customer’s browser to the URL provided, using a HTTP POST
request containing the URL encoded response.

For the Hosted Integration, this will redirect the Customer from the
Hosted Payment Page back to this URL on your website.

For the Direct Integration, this allows you to collect the Cardholder’s
payment details on your own server using a HTML form that POSTs to the
Direct Integration. which then effectively POSTs the results back to
this URL on your webserver, where you can display the transaction
outcome. This usage is not recommended as it makes it harder to sign the
message.

The URL is mandatory for the Hosted Integration and optional for the
Direct Integration. It is not supported by the Batch Integration.

The **redirectURL** must be a fully qualified URL, containing at least
the scheme and host components.

### Callback URL

The **callbackURL** request field allows you optionally to request that
the Gateway sends a copy of the response to an alternative URL. In this
case, each response will then be POSTed to this URL in addition to the
normal response. This allows you to specify a URL on a secure shopping
cart or backend order processing system, which will then fulfil any
order associated with the transaction.

The URL is optional for both the Hosted Integration and the Direct
Integration. It is not supported by the Batch Integration.

The **callbackURL** must be a fully qualified URL, containing at least
the scheme and host components.

### Field Formats

Most integration field values are either numerical or textual; and
either free format or from a range of predetermined values. Some field
values are records or arrays of records.

Unless otherwise stated, numerical values are whole integer values with
no decimal points. Textual values should use the UTF-8 character set and
will be automatically truncated if too long, unless stated otherwise in
the field’s description. Textual values may be transliterated[^1] when
sending to third parties such as Acquirers but the original value is
stored by Gateway and displayed in the Merchant Management System.

Field values should use the following formats unless otherwise stated in
the field’s description:

| **Field Type**         | **Value Format**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Monetary Amounts**   | Either major currency units by providing a value that includes a single decimal point such as ’10.99’; or in minor currency units by providing a value that contains no decimal points such as ‘1099’.                                                                                                                                                                                                                                                                                           |
| **Timestamps**         | Date in the format ‘YYYY-MM-DD HH:MM:SS’                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **Dates**              | Date in the format ‘YYYY-MM-DD’                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **Country Codes**      | Either the ISO-3166-1 2-letter, 3-letter or 3-digit code.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Currency Codes**     | Either the ISO-4217 3-letter or 3-digit code.                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Records**            | Records can be provided using the *\[XX\]* notation, where *XX* is the record’s field name (sub-field). Records can be multi-dimensional or be sequentially indexed. For example: to send a value for the sub-field *Y* in the integration field *X,* use the field name *X\[Y\]*; however, to send a value for the sub-field *Y* in the fourth record for integration field *X,* then use the field name *X\[4\]\[Y\]* etc. Boolean values must be sent as ‘true’ or ‘false’ as detailed below. |
| **Serialised Records** | Records can be sent as a JSON or URL serialised string. The first character of the serialised string determines its format: ‘{‘ indicates JSON format and anything else is assumed to be application/x-www-form-urlencoded format with any boolean values sent as ‘true’ or ‘false’ as detailed below.                                                                                                                                                                                           |

Note: Nested records are useful when posting sub-fields direct from a
HTML FORM. However, unlike the main integration fields, a nested
record’s sub-fields are not sorted when constructing the signature and
are processed in the order received. Serialised records can overcome any
problems caused by a nested record’s fields being received in a
different order to that used when generating the signature.

Boolean value cannot be represented using
application/x-www-form-urlencoded and are therefore avoid where
possible. However, some nested records may require the passing of
boolean values where the data is sent to or originates from an external
provider. In such circumstances any nested records boolean values must
be url encoded as the strings ‘true’ and ‘false’ and not ‘1’ and ‘0’. If
the nested records are being sent in JSON serialised format, then normal
JSON boolean types can be used.

##  Authentication

All requests must specify which Merchant Account they are for, using the
**merchantID** request field. In addition to this, the following
security measures can be used:

### Password Authentication

You can configure a password for each Merchant Account, using the
Merchant Management System (MMS). This password must then be sent in the
**merchantPwd** field in each request. If an incorrect password is
received by the Gateway, then the transaction will be aborted and an
error response is returned.

### Message signing

You can configure a signing secret phrase for each Merchant Account
using the Merchant Management System (MMS). When configured, each
request will need to be ‘signed’ by providing a **signature** field
containing a hash generated from the combination of the serialised
request and this signing secret phrase. On receipt, the Gateway will
then re-generate the hash and compare it with the one sent. If the two
hashes are different then the request received must not be the same as
that sent and so the contents must have been tampered with and the
transaction will be aborted and an error response is returned.

The Gateway will also return hash of the response message in the
returned **signature** field, allowing you to create your own hash of
the response (minus the **signature** field) and verify that the hashes
match.

**Message signing maybe mandatory on some Merchant Accounts and if so,
the Merchant Management System will only allow the secret phrase to be
changed but not removed entirely.**

If message signing is enabled, then the data POSTed to any callback URL
will also be signed.

See appendix A-13 for information on how to create the hash.

### Allowed IP addresses

You can configure a list of IP addresses using the Merchant Management
System (MMS). Two different address lists can be configured, one for
standard requests, such as sales; and one for advanced requests, such as
refunds and cancellations. If a request is received from an address
other than those configured, then it will be aborted and an error
response is returned.

##  Supported Actions

All requests must specify what action they require the Gateway to
perform, using the **action** request field. The Direct and Batch
Integrations support all actions; however, the Hosted Integration only
supports the basic payment actions.

### SALE

This will create a new transaction and attempt to seek authorisation for
a sale from the Acquirer. A successful authorisation will reserve the
funds on the Cardholder’s account until the transaction is settled.

The **captureDelay** field can be used to state whether the transaction
should be authorised only and settled at a later date. For more details
on delayed capture, refer to appendix A-10.

### VERIFY

This will create a new transaction and attempt to verify that the card
account exists with the Acquirer. The transaction will result in no
transfer of funds and no hold on any funds on the Cardholder’s account.
It cannot be captured and will not be settled. The transaction
**amount** must always be zero.

This transaction type is the preferred method for validating that the
card account exists and is in good standing; however, it cannot be used
to validate that it has sufficient funds.

### PREAUTH

This will create a new transaction and attempt to seek authorisation for
a sale from the Acquirer. If authorisation is approved, then it is
immediately voided (where possible) so that no funds are reserved on the
Cardholder’s account. The transaction will result in no transfer of
funds. It cannot be captured and will not be settled.

This transaction type can be used to check whether funds are available
and that the account is valid. However, due to the problem highlighted
below, it is recommended that Merchants use the VERIFY action when
supported by their Acquirer.

### REFUND_SALE

This will create a new transaction and attempt to seek authorisation for
a refund of a previous SALE from the Acquirer. The transaction will then
be captured and settled if and when appropriate. It can only be
performed on transactions that have been successfully settled. Up until
that point, a CANCEL or partial CAPTURE can be used, to refund or
partially refund the original SALE transaction. The previous SALE
transaction should be specified using the **xref** field.

Partial refunds are allowed by specifying the **amount** to refund. Any
amount must not be greater than the original received amount minus any
already refunded amount. Multiple partial refunds may be made while
there is still a portion of the originally received amount un-refunded.

The **captureDelay** field can be used to state whether the transaction
should be authorised only and settled at a later date. For more details
on delayed capture refer to appendix A-10.

***This action is not supported by the Hosted Integration.***

### REFUND

This will create a new transaction and attempt to seek authorisation for
a refund from the Acquirer. The transaction will then be captured and
settled if and when appropriate. This is an independent refund and need
not be related to any previous SALE. The amount is therefore not limited
by any original received amount.

The **captureDelay** field can be used to state whether the transaction
should be authorised only and settled at a later date. For more details
on delayed capture refer to appendix A-10.

***This action is not supported by the Hosted Integration.***

###  CAPTURE

This will capture an existing transaction, identified using the **xref**
request field, making it available for settlement at the next available
opportunity. It can only be performed on transactions that have been
authorised but not yet captured. An **amount** to capture may be
specified but must not exceed the original amount authorised.

The original transaction must have been submitted with a
**captureDelay** value that prevented immediate capture and settlement
leaving the transaction in an authorised but un-captured state. For more
details on delayed capture refer to appendix A-10.

***This action is not supported by the Hosted Integration.***

### CANCEL

This will cancel an existing transaction, identified using the **xref**
request field, preventing it from being settled. It can only be
performed on transactions, which have been authorised but not yet
settled, and it is not reversible. Depending on the Acquirer it may not
reverse the authorisation and release any reserved funds on the
Cardholder’s account. In such cases, authorisation will be left to
expire as normal releasing the reserved funds. This may take up to 30
days from the date of authorisation.

***This action is not supported by the Hosted Integration.***

### QUERY

This will query an existing transaction, identified using the **xref**
request field, returning the original response. This is a simple
transaction lookup action.

***This action is not supported by the Hosted Integration.***

# New Transactions

You can perform a new transaction, such as a sale, by sending a request
with the required action and transaction type together with details
about the order and payment method.

## Request Fields

<table>
<colgroup>
<col style="width: 35%" />
<col style="width: 17%" />
<col style="width: 47%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Field Name</strong></th>
<th><strong>Mandatory?</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>merchantID</strong></td>
<td>Yes</td>
<td>Your Gateway Merchant Account ID.</td>
</tr>
<tr class="even">
<td><strong>merchantPwd</strong></td>
<td>No<a href="#fn1" class="footnote-ref" id="fnref1"
role="doc-noteref"><sup>1</sup></a></td>
<td><p>Any password used to secure this account.</p>
<p>Refer to section 1.6.1 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>signature</strong></td>
<td>Yes<a href="#fn2" class="footnote-ref" id="fnref2"
role="doc-noteref"><sup>2</sup></a></td>
<td><p>Any hash used to sign this request.</p>
<p>Refer to section 1.6.2 for details.</p></td>
</tr>
<tr class="even">
<td><strong>action</strong></td>
<td>Yes</td>
<td><p>The action requested.</p>
<p>Refer to section 1.7 for supported actions.</p>
<p>Possible values are: <strong>PREAUTH, VERIFY, SALE, REFUND,
REFUND_SALE.</strong></p></td>
</tr>
<tr class="odd">
<td><strong>amount</strong></td>
<td>Yes<a href="#fn3" class="footnote-ref" id="fnref3"
role="doc-noteref"><sup>3</sup></a></td>
<td>The amount of the transaction.</td>
</tr>
<tr class="even">
<td><strong>type</strong></td>
<td>Yes<sup>3</sup></td>
<td><p>The type of transaction.</p>
<p>Refer to appendix A-15 for details.</p>
<p>Possible values are:</p>
<p><strong>1</strong> – E-commerce (ECOM)</p>
<p><strong>2</strong> – Mail Order/Telephone Order (MOTO).</p>
<p><strong>9</strong> – Continuous Authority (CA).</p></td>
</tr>
<tr class="odd">
<td><strong>countryCode</strong></td>
<td>Yes<sup>3</sup></td>
<td>Merchant’s location.</td>
</tr>
<tr class="even">
<td><strong>currencyCode</strong></td>
<td>Yes<sup>3</sup></td>
<td>Transaction currency.</td>
</tr>
<tr class="odd">
<td><strong>paymentMethod</strong></td>
<td>No</td>
<td>The payment method required. For card payments either omit this
field or use the value <strong>card</strong>.</td>
</tr>
<tr class="even">
<td><strong>cardNumber</strong></td>
<td>Yes<sup>3,</sup><a href="#fn4" class="footnote-ref" id="fnref4"
role="doc-noteref"><sup>4</sup></a></td>
<td>The primary account number (PAN) as printed on the front of the
payment card. Digits and spaces only.</td>
</tr>
<tr class="odd">
<td><strong>cardExpiryMonth</strong></td>
<td>Yes<sup>3,4</sup></td>
<td>Payment card’s expiry month from 1 to 12.</td>
</tr>
<tr class="even">
<td><strong>cardExpiryYear</strong></td>
<td>Yes<sup>3,4</sup></td>
<td>Payment card’s expiry year from 00 to 99.</td>
</tr>
<tr class="odd">
<td><strong>cardExpiryDate</strong></td>
<td>No<sup>3,4</sup></td>
<td>Payment card’s expiry date in MMYY format as an alternative to
sending a separate <strong>cardExpiryMonth</strong> and
<strong>cardExpiryYear</strong>.</td>
</tr>
<tr class="even">
<td><strong>cardCVV</strong></td>
<td>Yes<sup>3,4</sup></td>
<td>Payment card’s security number. The 3-digit number printed on the
signature strip.</td>
</tr>
<tr class="odd">
<td><strong>transactionUnique</strong></td>
<td>No<sup>3</sup></td>
<td>You can supply a unique identifier for this transaction. This is an
added security feature to combat transaction spoofing.</td>
</tr>
<tr class="even">
<td><strong>orderRef</strong></td>
<td>No<sup>3</sup></td>
<td>Free format text field to store order details, reference numbers,
etc. for the Merchant’s records.</td>
</tr>
<tr class="odd">
<td><strong>orderDate</strong></td>
<td>No</td>
<td>Optional date to record with the transaction.</td>
</tr>
<tr class="even">
<td><strong>captureDelay</strong></td>
<td>No</td>
<td><p>Number of days to wait between authorisation of a payment and
subsequent settlement.</p>
<p>Refer to appendix A-10 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>xref</strong></td>
<td>No<a href="#fn5" class="footnote-ref" id="fnref5"
role="doc-noteref"><sup>5</sup></a></td>
<td><p>Reference to a previous transaction.</p>
<p>Refer to appendix A-16 for details.</p></td>
</tr>
<tr class="even">
<td><strong>redirectURL</strong></td>
<td>No<sup>6</sup></td>
<td><p>A public URL which the hosted form will redirect the Customer’s
browser after the transaction has been completed. The URL must be fully
qualified and include at least the scheme and host components.</p>
<p>Refer to section 1.5.6 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>callbackURL</strong></td>
<td>No<a href="#fn6" class="footnote-ref" id="fnref6"
role="doc-noteref"><sup>6</sup></a></td>
<td><p>A non-public URL which will receive a copy of the transaction
result by POST.</p>
<p>The URL must be fully qualified and include at least the scheme and
host components.</p>
<p>Refer to section 1.5.7 for details.</p></td>
</tr>
<tr class="even">
<td><strong>remoteAddress</strong></td>
<td>No<a href="#fn7" class="footnote-ref" id="fnref7"
role="doc-noteref"><sup>7</sup></a></td>
<td>IP address of client making the transaction. This should be provided
where possible to aid fraud prevention.</td>
</tr>
<tr class="odd">
<td><strong>rtAgreementType</strong></td>
<td>No<a href="#fn8" class="footnote-ref" id="fnref8"
role="doc-noteref"><sup>8</sup></a></td>
<td><p>Agreement between Merchant and Cardholder for the storage of card
details.</p>
<p>Refer to appendix A-17 and A-19 for further details.</p>
<p>Possible values are: <strong>cardonfile</strong>,
<strong>instalment</strong>, <strong>recurring</strong>,
<strong>unscheduled</strong>, <strong>resubmission</strong>,
<strong>reauthorisation</strong>, <strong>delayedcharges</strong>,
<strong>noshow</strong></p></td>
</tr>
<tr class="even">
<td><strong>rtSequenceCount</strong></td>
<td>No<a href="#fn9" class="footnote-ref" id="fnref9"
role="doc-noteref"><sup>9</sup></a></td>
<td>Number of transactions in a recurring sequence if known. Refer to
appendix A-17 for further details.</td>
</tr>
<tr class="odd">
<td><strong>rtSequenceNumber</strong></td>
<td>No<sup>9</sup></td>
<td>Transaction number in a recurring sequence if known. Refer to
appendix A-17 for further details.</td>
</tr>
</tbody>
</table>
<aside id="footnotes" class="footnotes footnotes-end-of-document"
role="doc-endnotes">
<hr />
<ol>
<li id="fn1"><p>A password is not recommended if using the Hosted
Integration, use a signature instead.<a href="#fnref1"
class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn2"><p>A signature maybe mandatory on some Merchant Accounts
and requests.<a href="#fnref2" class="footnote-back"
role="doc-backlink">↩︎</a></p></li>
<li id="fn3"><p>Optional if an <strong>xref</strong> is provided as the
value will be taken from the cross-referenced transaction.<a
href="#fnref3" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn4"><p>Optional if using the Hosted Integration, any value
provided will be used to initialise any HPP input field.<a
href="#fnref4" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn5"><p>Mandatory for a REFUND_SALE request to specify the
original SALE transaction.<a href="#fnref5" class="footnote-back"
role="doc-backlink">↩︎</a></p></li>
<li id="fn6"><p>Not supported by the Batch Integration.<a href="#fnref6"
class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn7"><p>Not supported by the Hosted Integration, which will
automatically use the Customer’s IP address.<a href="#fnref7"
class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn8"><p>Mandatory for recurring transactions or other
transactions using stored credentials.<a href="#fnref8"
class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn9"><p>Mandatory for some Acquirers, its use is highly
recommended with recurring transactions.<a href="#fnref9"
class="footnote-back" role="doc-backlink">↩︎</a></p></li>
</ol>
</aside>

If the REFUND_SALE action is used, then the request must not attempt to
change the payment details, or the request will fail with a
**responseCode** of **65542 (REQUEST MISMATCH)** because the refund must
be made to the original card.

## Response Fields

The response will contain all the fields sent in the request (minus any
**cardNumber** and **cardCVV**) plus the following:

<table>
<colgroup>
<col style="width: 36%" />
<col style="width: 15%" />
<col style="width: 47%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Field Name</strong></th>
<th><strong>Returned?</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>responseCode</strong></td>
<td>Always</td>
<td><p>A numeric code providing the specific outcome.</p>
<p>Common values are:</p>
<p><strong>0</strong> - Successful / authorised transaction.</p>
<p><strong>1</strong> - Card referred – Refer to card issuer.</p>
<p><strong>2</strong> - Card referred – Special condition.</p>
<p><strong>4</strong> - Card declined – Keep card.</p>
<p><strong>5</strong> - Card declined.</p>
<p>Check <strong>responseMessage</strong> for more details of any error
that occurred.</p>
<p>Refer to appendix A-1 for details.</p></td>
</tr>
<tr class="even">
<td><strong>responseStatus</strong></td>
<td>Always</td>
<td><p>A numeric code providing the outcome category.</p>
<p>Possible values are:</p>
<p><strong>0</strong> – Authorisation Approved / No reason to
decline</p>
<p><strong>1</strong> – Authorisation Declined.</p>
<p><strong>2</strong> – Authorisation Error / Transaction
malformed.</p></td>
</tr>
<tr class="odd">
<td><strong>responseMessage</strong></td>
<td>Always</td>
<td>Message received from the Acquiring bank, or any error message.</td>
</tr>
<tr class="even">
<td><strong>transactionID</strong></td>
<td>Always</td>
<td>A unique ID assigned by the Gateway.</td>
</tr>
<tr class="odd">
<td><strong>xref</strong></td>
<td>Always</td>
<td><p>You may store the cross reference for repeat transactions.</p>
<p>Refer to appendix A-16 for details.</p></td>
</tr>
<tr class="even">
<td><strong>state</strong></td>
<td>Always</td>
<td><p>Transaction state.</p>
<p>Refer to appendix A-14.2 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>timestamp</strong></td>
<td>Always</td>
<td>Time the transaction was created or last modified.</td>
</tr>
<tr class="even">
<td><strong>transactionUnique</strong></td>
<td>If supplied</td>
<td>Any value supplied in the initial request.</td>
</tr>
<tr class="odd">
<td><strong>authorisationCode</strong></td>
<td>On success</td>
<td>Authorisation code received from Acquirer.</td>
</tr>
<tr class="even">
<td><strong>referralPhone</strong></td>
<td>If provided</td>
<td>Telephone number supplied by Acquirer to phone for voice
authorisation when provided.</td>
</tr>
<tr class="odd">
<td><strong>amountReceived</strong></td>
<td>On success</td>
<td>Amount the Acquirer authorised. This should always be the full
<strong>amount</strong> requested.</td>
</tr>
<tr class="even">
<td><strong>amountRefunded</strong></td>
<td>If refund</td>
<td>Total amount of original SALE that has so far been refunded.
Returned when <strong>action</strong> is REFUND_SALE.</td>
</tr>
<tr class="odd">
<td><strong>orderRef</strong></td>
<td>If supplied</td>
<td>Any value supplied in the initial request.</td>
</tr>
<tr class="even">
<td><strong>cardNumberMask</strong></td>
<td>Always</td>
<td>Card number masked for Merchant storage.</td>
</tr>
<tr class="odd">
<td><strong>cardTypeCode</strong></td>
<td>Always</td>
<td><p>Code identifying the type of card used.</p>
<p>Refer to appendix A-10 for details.</p></td>
</tr>
<tr class="even">
<td><strong>cardType</strong></td>
<td>Always</td>
<td><p>Description of the type of card used.</p>
<p>Refer to appendix A-10 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>cardSchemeCode</strong></td>
<td>Always</td>
<td><p>Code identifying the Card Scheme used.</p>
<p>Refer to appendix A-10 for details.</p></td>
</tr>
<tr class="even">
<td><strong>cardScheme</strong></td>
<td>Always</td>
<td><p>Description of the card scheme used.</p>
<p>Refer to appendix A-10 for details.</p></td>
</tr>
<tr class="odd">
<td><strong>cardIssuer</strong></td>
<td>Always</td>
<td>Card Issues name (when known).</td>
</tr>
<tr class="even">
<td><strong>cardIssuerCountry</strong></td>
<td>Always</td>
<td>Card issuing country’s name (when known).</td>
</tr>
<tr class="odd">
<td><strong>cardIssuerCountryCode</strong></td>
<td>Always</td>
<td>Card issuing country’s ISO-3166 2-letter code (when known).</td>
</tr>
<tr class="even">
<td><strong>acquirerResponseCode</strong></td>
<td>Conditional</td>
<td>Response code supplied by the Acquirer, maybe prefixed with ‘G:’ if
the Acquirer is itself a payment Gateway.</td>
</tr>
<tr class="odd">
<td><strong>acquirerResponseMessage</strong></td>
<td>Conditional</td>
<td>Response message supplied the Acquirer.</td>
</tr>
<tr class="even">
<td><strong>acquirerResponseDetails</strong></td>
<td>Conditional</td>
<td>Details about the Acquirer response containing any error messages
and codes. This can be used together with the normal
<strong>responseCode</strong>/<strong>responseMessage</strong> response
fields to further determine the reason for any failure.</td>
</tr>
<tr class="odd">
<td><strong>acquirerTransactionID</strong></td>
<td>Conditional</td>
<td>Transaction identifier/reference used to identify the transaction in
the Acquirer’s system.</td>
</tr>
</tbody>
</table>

Other response fields may be returned as documented elsewhere in this
guide. Undocumented fields may be returned at the Gateways discretion
but should not be relied upon.

The **acquirerResponse*XXXX*** fields are dependent on the Acquirer in
use and are supplied for additional information only.

The response is also POSTed to any URL provided by optional
**callbackURL**.

####### Integration Libraries

We can provide a range of libraries to help you to integrate with the
Gateway.

These libraries include simple sever-side classes in many popular
programming languages through to client-side scripts to help with the
integration of the Hosted Payment Page or Hosted Payment Fields.

The server-side libraries can be obtained by contacting customer
support.

The client-side libraries can be downloaded directly from the Gateway
server.

[^1]: Transliteration involves the changing of character case, stripping
    of accents from characters and removal of unsupported characters so
    that the values meet the requirements of the third-party.
