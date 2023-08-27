# This is my package chapaar

[![Latest Version on Packagist](https://img.shields.io/packagist/v/TookanTech/chapaar.svg?style=flat-square)](https://packagist.org/packages/TookanTech/chapaar)
[![GitHub Tests Action Status](https://img.shields.io/github/actions/workflow/status/TookanTech/chapaar/run-tests.yml?branch=main&label=tests&style=flat-square)](https://github.com/TookanTech/chapaar/actions?query=workflow%3Arun-tests+branch%3Amain)
[![GitHub Code Style Action Status](https://img.shields.io/github/actions/workflow/status/TookanTech/chapaar/fix-php-code-style-issues.yml?branch=main&label=code%20style&style=flat-square)](https://github.com/TookanTech/chapaar/actions?query=workflow%3A"Fix+PHP+code+style+issues"+branch%3Amain)
[![Total Downloads](https://img.shields.io/packagist/dt/TookanTech/chapaar.svg?style=flat-square)](https://packagist.org/packages/TookanTech/chapaar)

This package provides a flexible way to send and verify messages through various SMS providers. It offers integration with multiple drivers, making it easy to use different SMS service providers without getting locked into a specific one.

## Available Drivers
* Kavenegar (tested)
* SmsIr (tested)
* Ghasedak (under maintenance)
* Farapayamk (coming soon)
* Twillo (comming soon)


## Installation

You can install the package via composer:

```bash
composer require tookantech/chapaar
```


## Configuration

You can publish the config file with:

```bash
php artisan vendor:publish --tag="chapaar-config"
```
This will create a chapaar.php configuration file in your config directory, where you can set the default driver and configure driver-specific settings.

This is the contents of the published config file:
```php
return [
    'default' => env('CHAPAAR_DRIVER', 'kavenegar'),

    'drivers' => [
        'kavenegar' => [
            'method' => 'post',
            'scheme' => 'http',
            'api_key' => '',
            'line_number' => '1000689696',
        ],
        'smsir' => [
            'version' => 'v1',
            'api_key' => '',
            'line_number' => '1000689696',
        ],
    ],
];

```

## Usage
Using the Chapaar Facade

If you want to use the Chapaar package without Laravel's built-in notification system, you can utilize the Chapaar facade directly. This allows you to send and verify messages using different SMS providers.

Sending a Simple Message
```php
use TookanTech\Chapaar\Facades\Chapaar;
use TookanTech\Chapaar\SmsMessage;

$message = (new SmsMessage())->driver()
    ->setFrom('12345678')
    ->setTo('0912111111')
    ->setContent('Hello, this is a test message.');

$response = Chapaar::send($message);

```

Sending With Template Message
```php
use TookanTech\Chapaar\Facades\Chapaar;
use TookanTech\Chapaar\SmsMessage;
$message =(new SmsMessage())
    ->driver()
    ->setTemplate("invoice-paid")
    ->setTo('recipient_number')
    ->setTokens(['123','456','789']);
    
# SmsIr
$message
    ->driver()
    ->setTemplate("invoice-paid")
    ->setTo('recipient_number')
    ->setTokens([
        ['name' => 'code' , 'value' => '123']
    ]);

$response = Chapaar::verify($message);

```
## Using In Notifications
Please review [laravel notifications](https://laravel.com/docs/10.x/notifications) on how use notifications in laravel.

```php
use TookanTech\Chapaar\SmsChannel;
class InvoicePaid extends KavenegarBaseNotification
{
    public function via($notifiable): array
    {
        return [SmsChannel::class];
    }
    public function toSms($notifiable)
    {
        return (new SmsMessage)->driver()
        ->setTemplate('verify')
        ->setTokens([123],[456])
    }
}

class User extends Authenticatable
{
    use Notifiable;

    public function routeNotificationForSms($driver, $notification = null)
    {
        return $this->mobile;
    }

}
``` 

## Testing

```bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security Vulnerabilities

Please review [our security policy](../../security/policy) on how to report security vulnerabilities.

## Credits
- [tookantech](https://github.com/TookanTech)
- [arya](https://github.com/TookanTech)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
