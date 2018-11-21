# SoTellUs Provider for OAuth 2.0 Client

This package provides SoTellUs OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require sotellus/oauth2-sotellus
```

## Usage

Usage is the same as The League's OAuth client, using `\SoTellUs\OAuth2\Client\Provider\SoTellUs` as the provider.

### Authorization Code Flow

```php
$provider = new SoTellUs\OAuth2\Client\Provider\SoTellUs([
    'clientId'          => '{sotellus-client-id}',
    'clientSecret'      => '{sotellus-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
    'host'              => 'https://api.sotellus.com' // Defaults to https://api.sotellus.com
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getId());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Refreshing a Token

The SoTellUs API supports refresh tokens. Review the "[Refreshing a Token documentation](https://github.com/thephpleague/oauth2-client#refreshing-a-token)" on the base `oauth2-client` project for tips on implementing refresh tokens in your project.

## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](https://github.com/sotellus/oauth2-sotellus/blob/master/CONTRIBUTING.md) for details.


## Credits

- [Steven Maguire](https://github.com/sotellus)
- [All Contributors](https://github.com/sotellus/oauth2-sotellus/contributors)


## License

The MIT License (MIT). Please see [License File](https://github.com/sotellus/oauth2-sotellus/blob/master/LICENSE) for more information.
