# Payment Checkout

## Integrating Checkout

ShieldGate Checkout, simplifies and secures online payment processing. Quickly integrate Checkout into your site to provide your users with a
streamlined, mobile-ready payment experience that is constantly improving.

The easiest way to integrate ShieldGate is via Checkout, an embedded tool that takes care of building an HTML form, validating user input,
and securing your customers' card data. Using Checkout, sensitive credit card information is sent directly to ShieldGate, and does not touch your server. ShieldGate returns to your site a transaction object with the result of the operation.

To see Checkout in action, click the button above, filling in the resulting form with:

* Any random, syntactically valid email address (the more random, the better)
* Any Phone Number, such as 777777777
* Any Card Holder´s Name
* One of [ShieldGate's test card numbers](https://shieldgate.github.io/api-doc/#test-cards), such as 4111111111111111
* Any three-digit CVC code
* Any expiration date in the future

[View working example >](https://developers.shieldgate.mx/docs/payments/#checkout)


## Integration

The custom integration requires solid JavaScript skills.

When your page loads, you should create a handler object using `paymentCheckout.modal()`. You can then call `open()` on the handler in
 response to any event. If you need to abort the Checkout process—for example, when navigation occurs in a single-page application,
 call `close()` on the handler.


``` html
<!DOCTYPE html>
<html>
<head>
  <title>Example | Payment Checkout Js</title>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.shieldgate.mx/ccapi/sdk/payment_checkout_stable.min.js"></script>
</head>
<body>
<button class="js-payment-checkout">Pay with Card</button>
<div id="response"></div>

<script>
  let paymentCheckout = new PaymentCheckout.modal({
    client_app_code: 'PAYMENT_CLIENT_APP_CODE', // Client Credentials
    client_app_key: 'PAYMENT_CLIENT_APP_KEY', // Client Credentials
    locale: 'es', // User's preferred language (es, en, pt). English will be used by default.
    env_mode: 'stg', // `prod`, `stg`, `local` to change environment. Default is `stg`
    onOpen: function () {
      console.log('modal open');
    },
    onClose: function () {
      console.log('modal closed');
    },
    onResponse: function (response) { // The callback to invoke when the Checkout process is completed

      /*
        In Case of an error, this will be the response.
        response = {
          "error": {
            "type": "Server Error",
            "help": "Try Again Later",
            "description": "Sorry, there was a problem loading Checkout."
          }
        }

        When the User completes all the Flow in the Checkout, this will be the response.
        response = {
          "transaction":{
              "status": "success", // success or failure
              "id": "CB-81011", // transaction_id
              "status_detail": 3 // for the status detail please refer to: https://shieldgate.github.io/api-doc/#status-details
          }
        }
      */
      console.log('modal response');
      document.getElementById('response').innerHTML = JSON.stringify(response);
    }
  });

  let btnOpenCheckout = document.querySelector('.js-payment-checkout');
  btnOpenCheckout.addEventListener('click', function () {
    paymentCheckout.open({
      user_id: '1234',
      user_email: 'jhon@doe.com', //optional
      user_phone: '7777777777', //optional
      order_description: '1 Green Salad',
      order_amount: 1500,
      order_vat: 0,
      order_reference: '#234323411',
      //order_installments_type: 2, // optional: The installments type are only available for Equador. The valid values are: https://shieldgate.github.io/api-doc/#installments-type
      //order_taxable_amount: 0, // optional: Only available for Datafast (Equador). The taxable amount, if it is zero, it is calculated on the total. Format: Decimal with two fraction digits.
      //order_tax_percentage: 10 // optional: Only available for Datafast (Equador). The tax percentage to be applied to this order.
    });
  });

  window.addEventListener('popstate', function () {
    paymentCheckout.close();
  });
</script>
</body>
</html>

```

## Configuration options

Change how Checkout looks and behaves using the following configuration options.

### PaymentCheckout.modal
| Parameter       | Required | Description                                                                                              |
|-----------------|----------|----------------------------------------------------------------------------------------------------------|
| client_app_code | yes      | Client Credentials                                                                                       |
| client_app_key  | yes      | Client Credentials                                                                                       |
| env_mode        | yes      | `prod`, `stg`, `local` to change environment. Default is `stg`                                           |
| locale          | no       | User's preferred language (`es`, `en`, `pt`). English will be used by default.                           |
| onOpen          | no       | `function()` The callback to invoke when Checkout is opened                                              |
| onClose         | no       | `function()` The callback to invoke when Checkout is closed                                              |
| onResponse      | yes      | function([responseObject](#responseObject)) The callback to invoke when the Checkout process is complete |

#### responseObject

When the User completes all the Flow in the Checkout, this will be the response.
``` javascript
{  
   "transaction":{  
       "status":"success", // success or failure
        "id":"CB-81011", // transaction_id
        "status_detail":3 // for the status detail please refer to: https://paymentez.github.io/api-doc/#status-details
   }
}
```

In Case of an error, this will be the response.
``` javascript
{
  "error": {
    "type": "Server Error",
    "help": "Try Again Later",
    "description": "Sorry, there was a problem loading Checkout."
  }
}
```


### PaymentCheckout.open
| Parameter                      | Required | Description                                                                                                                                                      |
|--------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| user_id                        | yes      | Customer identifier. This is the identifier you use inside your application.                                                                                     |
| user_email                     | no       | If you already know the email address of your user, you can provide it to Checkout to be prefilled.                                                              |
| user_phone                     | no       | If you already know the phone of your user, you can provide it to Checkout to be prefilled.                                                                      |
| order_description              | yes      | A description of the product or service being purchased.                                                                                                         |
| order_amount                   | yes      | The amount that's shown to the user. Format: Decimal with two fraction digits.                                                                                   |
| order_vat                      | yes      | Sales tax amount, included in product cost. Format: Decimal with two fraction digits.                                                                            |
| order_reference                | yes      | Merchant order reference. You will identify this purchase using this reference.                                                                                  |
| order_installments_type        | no       | For Equador  the valid values are: https://shieldgate.github.io/api-doc/#installments-type.  For the rest of the countries, 0 to allow installments, -1 otherwise.|
| order_taxable_amount           | no       | Only available for Datafast (Equador). The taxable amount, if it is zero, it is calculated on the total. Format: Decimal with two fraction digits.               |
| order_tax_percentage           | no       | Only available for Datafast (Equador). The tax percentage to be applied to this order.                                                                           |
| conf_exclusive_types           | no       | Allowed card types to this operation.                                                                                                                            |
| conf_invalid_card_type_message | no       | Define a custom message to show for invalid card types.                                                                                                          |


## HTTPS requirements

All submissions of payment info using Checkout are made via a secure HTTPS connection. However, in order to protect yourself from certain
 forms of man-in-the-middle attacks, you must serve the page containing the payment form over HTTPS as well. In short, the address of the
  page containing Checkout must start with `https://` rather than just `http://`.

## Supported browsers

Checkout strives to support all recent versions of major browsers. For the sake of security and providing the best experience to the
 majority of customers, we do not support browsers that are no longer receiving security updates and represent a small minority of traffic.


## Prevent Checkout from being blocked

You can prevent Checkout's popup from being blocked by calling `paymentCheckout.open` when the user clicks on an element on the page.
 Do not call `paymentCheckout.open` in a callback. This design indicates to the browser that the user is explicitly requesting the popup.
  Otherwise, mobile devices and some versions of Internet Explorer will block the popup and prevent users from checking out.
