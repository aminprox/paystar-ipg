<p align="center"><a href="https://paystar.ir" target="_blank"><img src="https://paystar.ir/homepage/image/logo.svg" width="170" alt="PayStar Logo"></a></p>

<p align="center">This is a Laravel package for PayStar payment gateway.</p>

<div align="center">
    
[![Latest Version on Packagist](https://img.shields.io/packagist/v/paystar/laravel-ipg.svg?style=flat-square)](https://packagist.org/packages/paystar/laravel-ipg)
[![GitHub issues](https://img.shields.io/github/issues/aminprox/paystar-ipg?style=flat-square)](https://github.com/aminprox/paystar-ipg/issues)
[![GitHub stars](https://img.shields.io/github/stars/aminprox/paystar-ipg?style=flat-square)](https://github.com/aminprox/paystar-ipg/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/aminprox/paystar-ipg?style=flat-square)](https://github.com/aminprox/paystar-ipg/network)
[![Total Downloads](https://img.shields.io/packagist/dt/paystar/laravel-ipg.svg?style=flat-square)](https://packagist.org/packages/paystar/laravel-ipg)
[![GitHub license](https://img.shields.io/github/license/aminprox/paystar-ipg?style=flat-square)](https://github.com/aminprox/paystar-ipg/blob/master/LICENSE)
    
</div>

--------------------------

### :arrow_down: Installation guide

#### Install package
```bash
composer require paystar3/laravel-ipg
```
#### Publish configs

```bash
php artisan vendor:publish --tag=paystar-ipg
```

--------------------------

### :book: List of available methods
- <code>create()</code>: return a token
- <code>payment()</code>: auto redirect to gateway
- <code>verify()</code>: verify transaction

--------------------------

### :heavy_check_mark: How to use exists methods and options

- #### Use <code>create()</code> method
    ```php
    <?php
    use PayStar\Ipg\Facades\PayStarIpg;
    
    PayStarIpg::amount('AMOUNT') // *
        ->orderId('ORDER_ID') // *
        ->callbackUrl('CALLBACK_URL') // If You don't use this method, we set this from config
        ->sign('SIGN') // If You don't use this method, we generate auto a sign
        ->create();
    ```
    ###### List of extra option
    | Option  | description |
    |---| ------------- |
    | name  | customer name |
    | phone  | customer phone |
    | mail  | customer mail |
    | description  | description of order |
    | allotment  | Share per transaction |
    | callback_method  | - |
    | wallet_hashid  | - |
    | national_code  | national code of customer |
    
    ###### How to use this options
    ```php
    <?php
        use PayStar\Ipg\Driver\PayStar;
        use PayStar\Ipg\Facades\Encryption;
                $paystar = new PayStar();
                $res = $paystar->create($amount, $payment->id, route('callback', ['additional|_parameters' => 'additional_parameters']),null,
                [
                    'national_code' => 'National ID',
                    'card_number' => 'Card Number',
                    'callback_method' => 1
                ]);
                $result = $res->json();
                if ($result['status'] === 1) {
                    $payment->reference_id = $result['data']['ref_num'];
                    $payment->save();
                    return \redirect("https://api.paystar.shop/api/pardakht/payment?token={$result['data']['token']}");
                }
    ```

- #### Use <code>verify()</code> method
    ```php
    <?php
        use PayStar\Ipg\Driver\PayStar;
        use PayStar\Ipg\Facades\Encryption;
        $paystar = new PayStar();
        $sign = Encryption::hash(
                    config('paystar-ipg.encryption_algorithm'),
                    $payment->amount . '#'.
                    $request->input('ref_num') . '#'.
                    $request->input('card_number') . '#'.
                    $request->input('tracking_code'),
                    config('paystar-ipg.encryption_key')
        );
        if (request()->input('status') > 0 && !$last_4_digit_of_users_card_number != substr($request->input('card_number'), -4)) {
            throw new \Exception(__('Card doesnt belong to the user!'));
        }
        $res = $paystar->verify($payment->reference_id, $payment->amount, $sign);
        $result = $res->json();
        if ($result['status'] === 1) {
            $amount = $result['data']['price'];
            $trackingCode = $request->input('tracking_code');
        } else {
            throw new \Exception(__('Payment failed!'));
        }
    ```

--------------------------

### #️⃣ How to generate <code>sign</code>
```php
<?php
use PayStar\Ipg\Facades\Encryption;

// The Encryption Facade has 3 methods

Encryption::sign($amount, $orderId, $callbackUrl); // Generate a sign with set algorithm in config file

Encryption::algos(); // Show list of hash Algorithms (hash_algos() method)
Encryption::hash($algo, $string, $key, $binary); // use hash_hmac() method
```
  
--------------------------

### :man_technologist: Author

- [Github](https://github.com/aminprox)
- [linkedin](https://www.linkedin.com/in/amin-rezaei-sk)
