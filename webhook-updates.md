# Webhook & Updates

Working with Webhook Updates & Manually Fetching Updates (Long-polling).

## Getting Updates

There are two mutually exclusive ways of receiving updates for your bot — the `getUpdates()` method on one hand and Webhooks on the other. Incoming updates are stored on the Telegram server until the bot receives them either way, but they will not be kept longer than 24 hours.

Regardless of which option you choose, you will receive [Update](https://core.telegram.org/bots/api#update) objects as a result.

## Getting Updates Via Webhook

In order to receive updates via a Webhook, you first need to tell your webhook URL to Telegram. You can use `setWebhook($url)` method to specify a url and receive incoming updates via an outgoing webhook.

Whenever there is an update for the bot, Telegram will send an HTTPS POST request to the specified url, containing an Update object. In case of an unsuccessful request, Telegram will give up after a reasonable amount of attempts.

If you'd like to make sure that the Webhook request comes from Telegram, we recommend using a secret path in the URL, e.g. `https://www.example.com/<token>/webhook`. Since nobody else knows your bot's token, you can be pretty sure it's Telegram who made the request.

See [setWebhook](https://core.telegram.org/bots/api#setwebhook) docs for a list of supported parameters and other info.

### Setting Webhook

#### Standalone

```php
$response = $telegram->setWebhook(['url' => 'https://example.com/<token>/webhook']);

# Or if you are supplying a self-signed-certificate
$response = $telegram->setWebhook([
	'url' => 'https://example.com/<token>/webhook',
	'certificate' => '/path/to/public_key_certificate.pub'
]);
```

#### Laravel

Setup a POST route `/<token>/webhook`.

> **IMPORTANT:** You need to add your route in `$except` array inside the `app/Http/Middleware/VerifyCsrfToken.php` file in order to bypass the CSRF Token verification process that takes place whenever a POST route is called.

Here's an example based on the above case scenario:

```php
# app/Http/Middleware/VerifyCsrfToken.php

protected $except = [
    '/<token>/webhook'
];
```

Set webhook in Laravel:

```php
$response = Telegram::setWebhook(['url' => 'https://example.com/<token>/webhook']);

# Or if you are supplying a self-signed-certificate
$response = Telegram::setWebhook([
	'url' => 'https://example.com/<token>/webhook',
	'certificate' => '/path/to/public_key_certificate.pub'
]);
```

> **Notes**
>
> 1. You will not be able to receive updates using `getUpdates()` for as long as an outgoing webhook is set up.
> 2. To use a self-signed certificate, you need to upload your public key certificate using `certificate` parameter. Please pass the absolute path to the certificate when uploading.
> 3. Ports currently supported *for Webhooks*: **443, 80, 88, 8443**.
>
> If you're having any trouble setting up webhooks, please check out this [amazing guide to Webhooks](https://core.telegram.org/bots/webhooks).

### Getting Webhook Update

Once you set the webhook, You can then use the below function to retrieve the updates that are sent to your Webhook URL. The function returns an array of `Update` objects.

```php
# Standalone
$updates = $telegram->getWebhookUpdates();

# Laravel - Put this inside the POST route /<token>/webhook method.
$updates = Telegram::getWebhookUpdates();
```

### Removing Webhook

To remove a webhook (if it was set before).

```php
# Standalone
$response = $telegram->removeWebhook();

# Laravel
$response = Telegram::removeWebhook();
```

## Getting Updates Manually (Long Polling)

You can use `getUpdates()` method to receive incoming updates using long polling ([wiki](http://en.wikipedia.org/wiki/Push_technology#Long_polling)). An Array of Update objects is returned.

See [getUpdates](https://core.telegram.org/bots/api#getupdates
) docs for a list of supported parameters and other info.

```php
# Standalone
$response = $telegram->getUpdates();

# Laravel
$response = Telegram::getUpdates();
```

> **Notes**
>
> 1. This method will not work if an outgoing webhook is set up.
> 2. In order to avoid getting duplicate updates, recalculate *offset* after each server response.
