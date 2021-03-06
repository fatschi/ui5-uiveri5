# Authentication
To test a protected page, you need to specify authentication type and credentials. Authentication
is handled by plugable authenticator [modules](../src/moduleLoader.js). Basic (in URL), plain form, and form with UI5
authentication modules are already available. Each authenticator module accepts a number of parameters that could override the defaults.

## Authentication Configurations
To enable auth configuration, just configure it in the conf.js.
```javascript
auth: {
  // form based
  'sapcloud-form': {
      user: '<user>',
      pass: '<pass>'
  }
}
```

It is considered bad practice to leave credentials in the configs that may be saved in the source control system. 
The user and password parameters can be placeholders and the values can be passed from the command line.
```javascript
auth: {
  // form based
  'fiori-form': {
    user: '${params.user}',
    pass: '${params.pass}'
  }
}
```
To replace these placeholders, pass them in the command line:
```
uiveri5 --params.user=<user> --params.pass=<pass>
```

### Plain auth Configuration
This is the default authentication that is used whenever authentication is not configured explicitly.

### Basic auth Configuration
This configuration uses the `Basic-URL-Authenticator` and targets systems that use basic auth in the URL.

### Fiori-form auth Configuration
This configuration uses the `UI5-Form-Authenticator` and targets applications started from SAP Fiori Launchpad.

### Sapcp-form auth Configuration
This configuration uses the `Form-Authenticator` and targets applications behind SAP Cloud, SAP IDM or any other OAuth2.0 or plain form authentication. 

## Customize auth Configuration
It's possible to override an arbitrary authenticator parameter from the authentication configuration.
```javascript
auth: {
  'sapcloud-form': {
      name: './authenticator/formAuthenticator',
      userFieldSelector: '<CSS selector of user input field>',
      passFieldSelector: '<CSS selector of password input field>',
      logonButtonSelector: '<CSS selector of submit button>',
      idpSelector: '<CSS selector of login link>',
      user: '<user>',
      pass: '<pass>'
    }
}
```

## Authenticator Modules

### Plain Authneticator
This is a default authenticator that doesn't do any authentication. It is used by default whenever authentication is not configured.
Implemented in [plainAuthenticator.js](../../src/authenticator/plainAuthenticator.js).

### Basic URL Authenticator
It sends the user and password in the URL.
Implemented in [basicUrlAuthenticator.js](../../src/authenticator/basicUrlAuthenticator.js).

#### Parameters
* user - username 
* pass - password

### Form Authenticator
It supports browser redirects and does not require login page implemented with UI5.
Implemented in [formAuthenticator.js](../../src/authenticator/formAuthenticator.js).

#### Parameters
* user - username 
* pass - password
* userFieldSelector - the CSS selctor for the user input field
* passFieldSelector  - the CSS selector for the password input field
* logonButtonSelector - the CSS selector for the submit button
* idpSelector - the CSS selector for the link to log in with a different ID provider
* frameSelector - if provided, the inoput fields are searched in this iFrame
* redirectUrl - if provided, it overides the basicUrl that is used to synchronize on page redirect that the identitty provider
  initiates after successfull authentication. Request arguments and fragment are removed when matching, RegExp is supported.

Redirect to URLs that are matched with regex:
```javascript
auth: {
  'sapcloud-form': {
      user: '<user>',
      pass: '<pass>',
      redirectUrl: /console\/tenant\_/
    }
}
```

### UI5 Form Authenticator
This authenticator fills the user and password fields of a login form created with ui5 controls. It does not support redirections.
Implemented in [ui5FormAuthenticator.js](../../src/authenticator/ui5FormAuthenticator.js).

#### Parameters
* user - username 
* pass - password
* userFieldSelector - the CSS selctor for the user input field
* passFieldSelector  - the CSS selector for the password input field
* logonButtonSelector - the css selector for the submit button

### Custom Authenticator
If you have an application that uses a custom authentication scheme, you can implement a custom authenticator. You can use one of the existing authenticators as a base and extend it with the required behavior. Then, reference it in the `name` parameter of your auth configuration.

If your authenticator has wider usage, consider contributing it by creating a pull request against this repo.

## Programatic Authentication
Set `baseUrl` to `null` to disable automatic page loading and declartive authentication configuration. From the test, call  `browser.get()` with the required URL.

You can override the default auth settings by providing an options object with the same syntax as in the conf.js file.
```javascript
browser.get(
  '<url>',{
    auth:{
      'sapcloud-form': {
        user: '<user>',
        pass: '<pass>'
      }
    }
  }
);
```
