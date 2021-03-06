# php-google-contacts-v3-api

PHP library for the Google Contacts API (v3)

## Installation & Dependencies

This package and its dependencies can be installed using `composer`. 

Just run `composer require rapidwebltd/php-google-contacts-v3-api`.

## Setup

1. Install required dependencies. See the 'Dependencies' section above.
2. Copy or rename `.config_blank.json` to `.config.json`. Note the dot (`.`) at the beginning of the file name.
3. Fill in the `clientID`, `clientSecret` and `redirectUri` in `.config.json`.
  * Note that `redirectUri` should be configure to point towards the `redirect-handler.php` file.
  * The `clientID` and `clientSecret` can be found in the Google Developers console at https://console.developers.google.com/ under 'APIs & auth' -> 'Credentials', after enabling the Contacts API.
4. Go to `authorise-application.php` in a web browser. This should give you a URL to authorise your application for Google Contacts.
5. Go to the authorisation URL provided by the previous step.
6. Accept the permissions requested on the page displayed. You should then be redirected back to the `redirect-handler.php` file.
7. The page generated by the `redirect-handler.php` file should then present you with a refresh token. Copy this into your `.config.json`.
8. Setup is done!

## Usage

After the library has been installed and the setup and account association steps have been completed, you can make use of the library.

If your framework does not do this for you, remember to include the require the `vendor/autoload.php` file on any pages you wish to make use of this library on.

### Retrieving Google Contacts

The following code will retrieve all contacts from the associated Google account.

```php
$contacts = rapidweb\googlecontacts\factories\ContactFactory::getAll();

var_dump($contacts);
```

The `ContactFactory::getAll()` method will return an array of `Contact` objects. The contact's details will be available as public member variables of these objects.

The `selfURL` contained within each `Contact` object is the unique reference to this particular contact. If you need to retrieve a specific contact in the future, you will need to store this `selfURL`.

To retrieve a specific contact (by its selfURL), use the following code.

```php
$selfURL = "...";

$contact = rapidweb\googlecontacts\factories\ContactFactory::getBySelfURL($selfURL);

var_dump($contact);
```

This `ContactFactory::getBySelfURL` method will return a single `Contact` object.

Google Contact properties are accessed as follows.

```php
$selfURL = "...";

$contact = rapidweb\googlecontacts\factories\ContactFactory::getBySelfURL($selfURL);

echo $contact->name;
echo $contact->phoneNumber;
echo $contact->email;
```

### Updating existing Google Contacts

The updating of Google Contacts using this library is done in a very object orientated manner.

You must first retrieve a `Contact` object using one of the methods mentioned previously. You can then modify the contact object's public member variables. To save these changes back to the Google Contacts service, you then pass the modified object to the `ContactFactory::submitUpdates($contact)` method.

The following code demonstrates in full retrieving a contact, modifying it and submitting the updates.

```php
$selfURL = "...";

$contact = rapidweb\googlecontacts\factories\ContactFactory::getBySelfURL($selfURL);

var_dump($contact);

$contact->name = 'Test';
$contact->phoneNumber = '07812363789';
$contact->email = 'test@example.com';

$contactAfterUpdate = rapidweb\googlecontacts\factories\ContactFactory::submitUpdates($contact);

var_dump($contactAfterUpdate);
```

### Creating new Google Contacts

Creating a new Google Contact is very easy. Simply call the `ContactFactory::create($name, $phoneNumber, $emailAddress)` method, passing through appropriate parameters. This method will return the created contact as a `Contact` object including its newly assigned `selfURL`.

```php
$name = "Frodo Baggins";
$phoneNumber = "06439111222";
$emailAddress = "frodo@example.com";

$newContact = rapidweb\googlecontacts\factories\ContactFactory::create($name, $phoneNumber, $emailAddress);
```

### Config file override

Each method has optional argument for config file override. It is useful when you want to use work with multiple Google accounts at the same time.

```php
$customConfig = (object) array(
    'clientID' => '<clientId which you get according to setup above>',
    'clientSecret' => '<clientSecret which you get according to setup above>',
    'redirectUri' => '<your redirect uri>',
    'developerKey' => '<developer key>',
    'refreshToken' => '<refresh token specific for google account>'
);

$contacts = ContactFactory::getAll($customConfig);
```

You have to define all variables as the original config is completely ignored. To be more precise, it doesn't have to exist at all.


## Examples

Take a look at the following files for basic examples of how to retrieve contacts. They can also be used to ensure you have currently associated your Google account with the library.

* test.php
* test_individual.php
