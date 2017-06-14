# AuthAzureEasyAuth
This [MediaWiki](https://www.mediawiki.org) extension allows you to leverage [Authentication / Authorization](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) of [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/). It is meant to run in App Service's environment and it won't likely run on your custom server.

## Installation
Installation of this plugin consists of two parts - enabling Authentication/Authorization in Azure App Service and then enabling this extension within MediaWiki itself.

### Configure Azure App Service
First, App Service Authentication/Authorization has to be enabled in Azure App Service. You can do that by following [the tutorial in official docs](https://docs.microsoft.com/en-us/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication).
#### Multitenant environment
If you would like to use this extension from within a multitenant environment - for example from multiple Azure Active Directory tenants, you have to do following steps:
- Mark the application in Azure AD as multitenant
- Add additional issuers to the `$wgAuthAzureEasyAuthIssuers` array.

### Configure MediaWiki
Next you need to configure your MediaWiki instance. You have to put following to your `LocalSettings.php`:
```php
####################################################
# Extension: AuthAzureEasyAuth
wfLoadExtension( 'AuthAzureEasyAuth' );
# List of valid issuers, in basic scenarios, this will contain only one entry.
$wgAuthAzureEasyAuthIssuers = [
    "https://sts.windows.net/{tenant-id}/",
];

# Make this wiki private and disable account creation to anonymous users.
$wgGroupPermissions['*']['createaccount'] = false;
$wgGroupPermissions['*']['read'] = false;
$wgGroupPermissions['*']['edit'] = false;
# Since manual user creation is disallowed, we should allow this extension to create users. If you don't want this extension to create users, set the option below to `false`
$wgGroupPermissions['*']['autocreateaccount'] = true;
# By default, MediaWiki has `@` as invalid character in username, we have to override it so e-mail addresses work as usernames.
$wgInvalidUsernameCharacters = '';
# Additionally, as per documentation `https://www.mediawiki.org/wiki/Manual:$wgInvalidUsernameCharacters` we should also override the delimiter for UserRights page.
$wgUserrightsInterwikiDelimiter = '<@>';
####################################################
```
#### Obtaining issuer URL
For Azure AD, this is very simple - it can be done either from [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) by copying the Directory ID and replacing `{tenant-id}` in the `$wgAuthAzureEasyAuthIssuers` array. It can also obtained by visiting [https://login.microsoftonline.com/{your-domain}/.well-known/openid-configuration](https://login.microsoftonline.com/{your-domain}/.well-known/openid-configuration) and copying `issuer` value from the JSON.

## Credits
Created by [TheNetw.org](https://thenetw.org), inspired by [Auth_RemoteUser](https://www.mediawiki.org/wiki/Extension:Auth_remoteuser) extension for MediaWiki.