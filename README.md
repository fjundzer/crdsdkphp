# Cardinity Client PHP library

This is an official PHP client library for <a href="https://developers.cardinity.com/api/v1/">Cardinity's</a> API.<br>
Library includes all the functionality provided by the API. Library was designed to be flexible and self-explanatory for developers to implement.

### Table of Contents  
[<b>Installation →</b>](#installation)   
      [via Composer](#installing-via-composer)   
      [Configuration](#configuration)  
      [Registering the bundle](#registering-the-bundle)  
      [Enable credit card processing with 3-D secure DEMO](#Enable-credit-card-processing-with-3-D-secure-DEMO)             
[<b>Usage →</b>](#usage)   
      [Services](#services)   
      [Available Methods](#available-methods)  
      [Available Queries](#available-queries)  
      [Voids](#voids)             
 [<b>Downloads →</b>](#downloads)<br>
 [<b>Having problems? →</b>](#having-problems)<br>
 [<b>About us →</b>](#-aboutus)<br>     
<a name="headers"/>  
### Official API documentation can be found [here](https://developers.cardinity.com/api/v1/).

## Installation
### Installing via [Composer](https://getcomposer.org)
```bash
$ php composer.phar require cardinity/client-bundle
```

### Configuration
To use the bundle you have to define two parameters in your `app/config/config.yml` file under `cardinity_client` section
```yaml
# app/config/config.yml
cardinity_client:
    consumer_key: key
    consumer_secret: secret
```

Where:
-   `consumer_key`: You can find your Consumer Key and Consumer Secret in Cardinity member’s area.
-   `consumer_secret`: You can find your Consumer Key and Consumer Secret in Cardinity member’s area.

### Registering the Bundle
You have to add `CardinityClientBundle` to your `AppKernel.php`:
```php
# app/AppKernel.php
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            // ... other bundles
            new Cardinity\ClientBundle\CardinityClientBundle()
        );

        return $bundles;
    }
}
```

### Enable credit card processing with 3-D secure DEMO
Include following lines to `app/config/routing.yml`:

```yaml
cardinity_client:
    resource: "@CardinityClientBundle/Resources/config/routing.yml"
    prefix: /cardinity
```

And if you are using PHP built-in web server:
```bash
    app/console server:run
```

Try to open browser with address `http://localhost:8000/cardinity`.

## Usage
### Services
This bundle comes with following service which simplifies the
Cardinity implementation in your project:
```php
/** @type Cardinity\Client */
$client = $this->container->get('cardinity_client.service.client');
```

### Available Methods
Validates and executes Cardinity query
```php
/** @type Cardinity\Method\ResultObjectInterface
$result = $client->call($query);
```

### Available Queries

#### Payment
```php
Cardinity\Payment\Create($body)
Cardinity\Payment\Finalize($paymentId, $authorizeData)
Cardinity\Payment\Get($paymentId)
Cardinity\Payment\GetAll($limit)
```

#### Settlement
```php
Cardinity\Settlement\Create($paymentId, $amount, $description = null)
Cardinity\Settlement\Get($paymentId, $settlementId)
Cardinity\Settlement\GetAll($paymentId)
```

#### Void
```php
Cardinity\Void\Create($paymentId, $description = null)
Cardinity\Void\Get($paymentId, $voidId)
Cardinity\Void\GetAll($paymentId)
```

#### Refund
```php
Cardinity\Refund\Create($paymentId, $amount, $description = null)
Cardinity\Refund\Get($paymentId, $refundId)
Cardinity\Refund\GetAll($paymentId)
```

#### Usage
```php
use Cardinity\Method\Payment;

/** @type Cardinity\Client */
$client = $this->container->get('cardinity_client.service.client');
try {
    /** @type Payment\Payment */
    $payment = $client->call(new Payment\Create([
        'amount' => 50.00,
        'currency' => 'EUR',
        'settle' => false,
        'description' => 'some description',
        'order_id' => '12345678',
        'country' => 'LT',
        'payment_method' => Cardinity\Payment\Create::CARD,
        'payment_instrument' => [
            'pan' => '4111111111111111',
            'exp_year' => 2018,
            'exp_month' => 12,
            'cvc' => '456',
            'holder' => 'Mike Dough'
        ]
    ]));

    /** @type Payment\Payment */
    $finalizedPayment = $client->call(new Payment\Finalize(
        $payment->getId(),
        $payment->getAuthorizationInformation()->getData()
    ));
} catch (Cardinity\Exception\Declined $e) {
    // Payment has been declined
} catch (Cardinity\Exception\Runtime $e) {
    // Other type of error happened
}
```

#### More usage examples available at [Cardinity PHP client repository](https://github.com/cardinity/cardinity-sdk-php).


## Downloads
<b>Latest (recommended) version</b>
  
| Version       | Description                                         |Link        |
| ------------- |-----------------------------------------------------|------------|
| 3.0.2         | <b>Latest (recommended) version.</b> | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v3.0.2">Download</a> |

<details show>
  <summary>Older releases</summary>
   
| Version       | Description                                         |Link        |
| ------------- |-----------------------------------------------------|------------|
| 3.0.1 | Backword compatibility issue fix | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v3.0.1">Download</a> |
| 3.0.0 | <b>Updated for 3D secure version 2</b><br><details show><summary>Added</summary>```threeDS2AuthorizationInformation``` property to ```Payment``` class<br>```getThreeds2data``` method to ```Payment``` class<br>```setThreeds2data``` method to ```Payment``` class<br>```isThreedsV1``` method to ```Payment``` class<br>```isThreedsV2``` method to ```Payment``` class<br>```getThreeDS2DataConstraints``` method to ```Create``` class<br>```getBrowserInfoConstraints``` method to ```Create``` class<br>```getAdressConstraints``` method to ```Create``` class<br>```getCardHolderInfoConstraints``` method to ```Create``` class<br>```buildElement``` method to ```Create``` class<br>```paymentId``` property to ```Finalize``` class<br>```finalizeKey``` property to ```Finalize``` class<br>```Method\Payment\ThreeDS2Data``` parameters<br>```Method\Payment\TreeDS2AthorizationInformation``` class</details><br><details show><summary>Changed</summary>Updated ```php``` to version 7.2.x<br>Updated ```symfony/validator``` to version 5.x<br>Updated ```phpspec/phpspec``` to version 6.2<br>Updated ```phpunit/phpunit``` to version 8.5<br>Updated ```symfony/yaml``` to version 4.4<br>Refactored ```Create``` class to build validation parameters using ```buildElement``` method<br>Updated ```getValidationConstraints``` method of ```Create``` class<br>Updated ```getPaymentInstrumentConstraints``` method of ```Create``` class<br>Updated ```__construct``` method of ```Finalize``` class<br>Updated ```getAttributes``` method of ```Finalize``` class<br>Updated ```getValidationConstraints``` method of ```Finalize``` class</details>| <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v3.0.0">Download</a> |
| 2.1.0 | Added: ```isDeclined``` method to ```Payment``` class <br> ```isApproved``` and ```isDeclined``` methods to Refund class. <br> ```isApproved``` and ```isDeclined``` methods to Settlement class. <br> ```isApproved``` and ```isDeclined``` methods to VoidPayment class. | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v2.1.0">Download</a> |
| 2.0.0 | Changes:<br>- Renamed Cardinity\Method\Void to Cardinity\Method\VoidPayment<br>- Renamed Void.php to VoidPayment.php<br>Removed<br>- ResultObjectPropertyNotFound.php| <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v2.0.0">Download</a> |
| 1.1.1 | Allow Symfony Validator 4<br>Remove exception throw when response object changes | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v1.1.1">Download</a> |
| 1.1.0 | Upgrade to Guzzle 6 | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v1.1.0">Download</a> |
| 1.0.3 | Get all payments default limit | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v1.0.3">Download</a> |
| 1.0.1         | Bug "Client::__construct() has too many arguments issue" fixed.  | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v1.0.2">Download</a> |
| 1.0.1 | Bigfixes. Fixed issue found on PHP 5.4 | <a href="https://github.com/cardinity/cardinity-sdk-php/releases/tag/v1.0.1">Download</a>
</details>

### Having problems?  

Feel free to contact us regarding any problems that occurred during integration via info@cardinity.com. We will be more than happy to help.


-----

### ► About us
Cardinity is a licensed payment institution, active in the European Union, registered on VISA Europe and MasterCard International associations to provide <b>e-commerce credit card processing services</b> for online merchants. We operate not only as a <u>payment gateway</u> but also as an <u>acquiring Bank</u>. With over 10 years of experience in providing reliable online payment services, we continue to grow and improve as a perfect payment service solution for your businesses. Cardinity is certified as PCI-DSS level 1 payment service provider and always assures a secure environment for transactions. We assure a safe and cost-effective, all-in-one online payment solution for e-commerce businesses and sole proprietorships.<br>
#### Our features
• Fast application and boarding procedure.   
• Global payments - accept payments in major currencies with credit and debit cards from customers all around the world.   
• Recurring billing for subscription or membership based sales.  
• One-click payments - let your customers purchase with a single click.   
• Mobile payments. Purchases made anywhere on any mobile device.   
• Payment gateway and free merchant account.   
• PCI DSS level 1 compliance and assured security with our enhanced protection measures.   
• Simple and transparent pricing model. Only pay per transaction and receive all the features for free.
### Get started
<a href="https://cardinity.com/sign-up">Click here</a> to sign-up and start accepting credit and debit card payments on your website or <a href="https://cardinity.com/company/contact-us">here</a> to contact us 
#### Keywords
payment gateway, credit card payment, online payment, credit card processing, online payment gateway, cardinity client bundle.     

  
 [▲ back to top](#Cardinity-Client-PHP-Library)
