# OmnipayBridge
[![Build Status](https://travis-ci.org/Payum/OmnipayBridge.png?branch=master)](https://travis-ci.org/Payum/OmnipayBridge) [![Total Downloads](https://poser.pugx.org/payum/omnipay-bridge/d/total.png)](https://packagist.org/packages/payum/omnipay-bridge) [![Latest Stable Version](https://poser.pugx.org/payum/omnipay-bridge/version.png)](https://packagist.org/packages/payum/omnipay-bridge)

[Omnipay](https://github.com/adrianmacneil/omnipay) created by [Adrian Macneil](http://adrianmacneil.com/). The lib provides unified api for 25+ gateway gateways. Plus, it simple, has unified, consistent API and fully covered with tests.
This bridge allows you to use omnipay gateways but in payum like way.

## Why should you use this bridge?

Here's an [example from omnipay's repository](https://github.com/thephpleague/omnipay#tldr):

```php
<?php
use Omnipay\Omnipay;

$gateway = Omnipay::create('Stripe');
$gateway->setApiKey('abc123');

$formData = ['number' => '4242424242424242', 'expiryMonth' => '6', 'expiryYear' => '2016', 'cvv' => '123'];
$response = $gateway->purchase(['amount' => '10.00', 'currency' => 'USD', 'card' => $formData])->send();

if ($response->isSuccessful()) {
    // payment was successful: update database
    print_r($response);
} elseif ($response->isRedirect()) {
    // redirect to offsite payment gateway
    $response->redirect();
} else {
    // payment failed: display message to customer
    echo $response->getMessage();
}
```

and this is same code but done with the bridge:

```php
<?php
use Payum\Core\PayumBuilder;
use Payum\Core\Request\Capture;
use Payum\Core\Request\GetHumanStatus;
use Payum\Core\Model\ArrayObject;

$payum = (new PayumBuilder())
    ->addDefaultStorages()
    ->addGateway('stripe', ['factory' => 'omnipay_stripe', 'apiKey' => 'abc123'])
    ->getPayum()
;

$card = ['number' => '4242424242424242', 'expiryMonth' => '6', 'expiryYear' => '2016', 'cvv' => '123'];
$payment = new ArrayObject(['amount' => '10.00', 'currency' => 'USD', 'card' => $card]);

if ($reply = $payum->getGateway('stripe')->execute(new Capture($payment), true)) {
    // convert reply to http response
}

$payum->getGateway('stripe')->execute($status = new GetHumanStatus($payment));
if ($status->isCaptured()) {
    // success
}
```

Well more or less same amount of code but with the bridge you get more out of the box:

* Return\Cancel urls are generated just of the box. The urls are unique and do not expose any sensitive information.
* If you do not pass credit card, Payum asks a user for it, showing the page with the form.
* You can use Payum's packages for Symfony,Laravel,Silex,Zend,Yii with the bridge.
* Storages. Your payment is already stored on the filesystem. We advice not to use this storage in prod.
* The payment model contains all the information we were able to get from omnipay. Just use it.
* Payum abstracts workflow. It knows when Omnipay's `purchase` or `purchaseComplete` methods should be used.
* Credit card details are protected from accidental storing on your side.
* Using the builder you can overwrite any part you want, or add a Payum extension.

## Resources

* [Documentation](https://github.com/Payum/Payum/blob/master/src/Payum/Core/Resources/docs/index.md)
* [Questions](http://stackoverflow.com/questions/tagged/payum)
* [Issue Tracker](https://github.com/Payum/Payum/issues)
* [Twitter](https://twitter.com/payumphp)

## Support me

There are lots of features were done and even more stuff in todo list. If the library made your life easier consider support me. You can eiether hire me to do some payment related stuff or donate. You can also send a mail with a feedback. [Tell me](https://github.com/makasim) about your expiriens with Payum.

<a href='https://pledgie.com/campaigns/30526'><img alt='Click here to lend your support to: Your private payment processing server. Setup it once and rule them all and make a donation at pledgie.com !' src='https://pledgie.com/campaigns/30526.png?skin_name=chrome' border='0' ></a>

## License

OmnipayBridge is released under the [MIT License](LICENSE).
