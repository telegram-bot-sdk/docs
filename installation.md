# Installation

## Requirements

- PHP >=5.6
- Telegram Bot API Token - Talk to [@BotFather](https://core.telegram.org/bots#6-botfather) and generate one
- Laravel >=5 (Optional)

## Installation

Telegram Bot SDK utilizes [Composer](https://getcomposer.org/) to manage its dependencies. So, before using the SDK, make sure you have Composer installed on your machine.

Install the Telegram Bot SDK using Composer:

```bash
$ composer require telegram-bot-sdk/telegram-bot-sdk
```

### Laravel Setup

Telegram Bot SDK will self-register its service provider and facade in Laravel >=5.5.

#### Laravel <5.5

If you're using Laravel `<5.5`, you'll manually have to register the service provider and facade.

##### Service Provider

```php
Telegram\Bot\Laravel\TelegramServiceProvider::class,
```

##### Facade (Optional)

```php
'Telegram' => Telegram\Bot\Laravel\Facades\Telegram::class,
```

#### Publish Configuration File

Publish the configuration file by running this command in your terminal window:

```bash
$ php artisan vendor:publish --provider="Telegram\Bot\Laravel\TelegramServiceProvider"
```

## Configuration

Before you can start building a Telegram Bot, you need to configure Telegram Bot SDK with your Bot Token that was provided by BotFather. Once you do that, you'll get access to all the available Bot API Methods to make requests to the Telegram Bot API.

### Standalone

```php
require __DIR__.'/vendor/autoload.php';

use Telegram\Bot\Api;

$telegram = new Api('YOUR BOT TOKEN');
```

### Laravel

Open the `config/telegram.php` configuration file and set the `bot_token` with your Telegram Bot Token or you can also set an environment variable `TELEGRAM_BOT_TOKEN` with the appropriate value.

Refer the configuration file to know more about the available options with detailed information.

In Laravel, you don't have to initilize the SDK as its taken care for you in the service provider. You can now get started to write your Telegram Bot.

## Usage

Here's an example of `getMe()` API method you can use to test your bot's auth token.
Returns basic information about the bot in form of a User object.

See [getMe](https://core.telegram.org/bots/api#getme) docs for more details.

### Laravel

#### Using Facade

```php
use Telegram\Bot\Laravel\Facades\Telegram;

$response = Telegram::getMe();
```

#### Using Service Container

Here's an example using Laravel's service container aka dependency injection.

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Telegram\Bot\Api;

class BotController extends Controller
{
    protected $telegram;

    /**
     * Create a new controller instance.
     *
     * @param  Api  $telegram
     */
    public function __construct(Api $telegram)
    {
        $this->telegram = $telegram;
    }

    /**
     * Show the bot information.
     */
    public function show()
    {
        $response = $this->telegram->getMe();

        return $response;
    }
}
```

## Handling Response

Telegram Bot API responses in the SDK are represented as PHP objects. Telegram Bot SDK supports all [available types](https://core.telegram.org/bots/api#available-types) of response objects with various helpful methods. In addition, all the related objects of an object are also automatically mapped to the appropriate PHP response object.

In the above example, `getMe()` returns a [User](https://core.telegram.org/bots/api#user) object.

So we can retrieve the object values like so:

```php
$botId = $response->getId();
$firstName = $response->getFirstName();
$username = $response->getUsername();
```

### Additional Information

Telegram Bot SDK takes advantage of the amazing Laravel Collection API to automatically map the data.

So it's a lot easier to work with the array of data. Supports all the methods listed on the [official docs](https://laravel.com/docs/5.1/collections).
