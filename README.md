
# SendGrid

* Contributors: Originally Based on SendGrid 
* Tags: none
* Requires at least: 5.2
* Tested up to: 5.2
* Stable tag: 1.12.0
* License: GPLv2 or later
* License URI: http://www.gnu.org/licenses/gpl-2.0.html

Modified version of original SendGrid connector.

## Description

Forked by Orangeleaf Systems to remove functionality and potential vulnerabilities

## Installation

Requirements:

1. PHP version >= 7.2 
2. To send emails through SMTP you need to install also the 'Swift Mailer' plugin.
3. If wp_mail() function has been declared by another plugin that you have installed, you won't be able to use the SendGrid plugin

### Global settings

SendGrid settings can optionally be defined as global variables (wp-config.php):

1. Set the API key. You need to make sure you set the Mail Send permissions to FULL ACCESS, Stats to READ ACCESS and Template Engine to READ or FULL ACCESS when you created the api key on SendGrid side, so you can send emails and see statistics on wordpress):
    * API key:  define('SENDGRID_API_KEY', 'sendgrid_api_key');

2. Set email related settings:
    * Send method ('api' or 'smtp'): define('SENDGRID_SEND_METHOD', 'api');
    * From name: define('SENDGRID_FROM_NAME', 'Example Name');
    * From email: define('SENDGRID_FROM_EMAIL', 'from_email@example.com');
    * Reply to email: define('SENDGRID_REPLY_TO', 'reply_to@example.com');
    * Categories: define('SENDGRID_CATEGORIES', 'category_1,category_2');
    * Template: define('SENDGRID_TEMPLATE', 'templateID');
    * Content-type: define('SENDGRID_CONTENT_TYPE', 'html');
    * Unsubscribe Group: define('SENDGRID_UNSUBSCRIBE_GROUP', 'unsubscribeGroupId');

3. Set widget related settings:
    * Marketing Campaigns API key: define('SENDGRID_MC_API_KEY', 'sendgrid_mc_api_key');
    * Use the same authentication as for sending emails ('true' or 'false'): define('SENDGRID_MC_OPT_USE_TRANSACTIONAL', 'false');
    * The contact list ID: define('SENDGRID_MC_LIST_ID', 'listID');
    * Display the first and last name fields ('true' or 'false'): define('SENDGRID_MC_OPT_INCL_FNAME_LNAME', 'true');
    * First and last name fields are required ('true' or 'false'): define('SENDGRID_MC_OPT_REQ_FNAME_LNAME', 'true');
    * Signup confirmation email subject: define('SENDGRID_MC_SIGNUP_EMAIL_SUBJECT', 'Confirm subscription');
    * Signup confirmation email content: define('SENDGRID_MC_SIGNUP_EMAIL_CONTENT', '&lt;a href="%confirmation_link%"&gt;click here&lt;/a&gt;');
    * Signup confirmation page ID: define('SENDGRID_MC_SIGNUP_CONFIRMATION_PAGE', 'page_id');

4. Other configuration options:
    * Set a custom timeout for API requests to SendGrid in seconds: define('SENDGRID_REQUEST_TIMEOUT', 10);


### Filters

Use HTML content type for a single email:

```
add_filter('wp_mail_content_type', 'set_html_content_type');

// Send the email 

remove_filter('wp_mail_content_type', 'set_html_content_type');
```

Change the email contents for all emails before they are sent:

```
function change_content( $message, $content_type ) {   
    if ( 'text/plain' == $content_type ) {
      $message = $message . ' will be sent as text ' ;
    } else {
      $message = $message . ' will be sent as text and HTML ';
    }

    return $message;
}

add_filter( 'sendgrid_override_template', 'change_content' );
```

Changing the text content of all emails before they are sent:

```
function change_sendgrid_text_email( $message ) {
    return $message . ' changed by way of text filter ';
}

add_filter( 'sendgrid_mail_text', 'change_sendgrid_text_email' );
```

Changing the HTML content of all emails before they are sent:

```
function change_sendgrid_html_email( $message ) {
    return $message . ' changed by way of html filter ';
}

add_filter( 'sendgrid_mail_html', 'change_sendgrid_html_email' );
```

Note that all HTML emails sent through our plugin also contain the HTML body in the text part and that content will pass through the "sendgrid_mail_text" filter as well.

## Frequently asked questions

### What permissions should my API keys have ?

For the API Key used for sending emails (the General tab):
 - Full Access to Mail Send.
 - Read Access to Stats.
 - Read Access to Supressions > Unsubscribe Groups.
 - Read Access to Template Engine.
For the API Key used for contact upload (the Subscription Widget tab):
 - Full Access to Marketing Campaigns.


### Can I disable the opt-in email?

No. SendGrid’s Email Policy requires all email addressing being sent to by SendGrid customers be confirmed opt-in addresses.

### Can I use this plugin with BuddyPress ?

Yes. Our plugin required special integration with BuddyPress and it's regularly tested to ensure it behaves as expected. If you have noticed issues caused by installing this plugin along with BuddyPress, you can add the following line to your wp-config.php to disable it :

`define('SENDGRID_DISABLE_BUDDYPRESS', '1');`

If you're trying to send plaintext emails using BuddyPress, keep in mind that by default the whitespace content of those emails is normalized.

That means that some newlines might be missing if you expect them to be there.

To disable this functionality, you need to add the following line in your wp-config.php file:

`define('SENDGRID_DISABLE_BP_NORMALIZE_WHITESPACE', '1');`

## Changelog

**1.12.0**
Forked.  Removed potentially vulnerable features.

**1.11.7**
* Added a configuration parameter of API request timeout in seconds
* Fixed an issue that made the HTML subscription emails break links

**1.11.6**
* Added a feature flag to disable whitespace normalization in BuddyPress plaintext emails
* Fixed an issue where the from name and email subjects would incorrectly display the ampersand symbol

**1.11.5**
* Fixed a potential stored XSS issue on the backend settings form
* Fixed a potential CSRF issue on the backend settings form

**1.11.4**
* Fixed an issue where TO field recipients could not see each other in the email header

**1.11.3**
* Fixed an issue where the send test form was displayed when no API key was set
* Fixed an issue where the subscription test form was not displayed for the default contact list
* Fixed an issue where the virtual pages for Subscription errors was not displayed
* Fixed an issue where there was no notification for option update on the Multisite settings page
* Fixed an issue where there was no notification when an API key was not set on the General tab when there was one on the Subscription Widget tab

**1.11.2**
* Relaxed PHP requirement to at least version 5.4.

**1.11.1**
* Confirmed compatibility with PHP 7 and 7.1
* Removed some legacy code that caused warnings in PHP 7
* Fixed issue where the statistics page would show up in menu even if the API key did not have stats permissions

**1.11.0**
* BREAKING CHANGE: DO NOT UPGRADE IF YOU USE PHP <= 5.3. Only PHP 5.4 and later versions are supported.
* BREAKING CHANGE: Username & Password is no longer supported. Change your settings to use an API Key before updating
* API Mail Send was changed to use the V3 SendGrid API
* Emails sent with the V2 Email Object will now be translated to V3
* BREAKING CHANGE: The date parameter on the V2 object is no longer supported
* BREAKING CHANGE: When using the V2 object with SMTPAPI Tos, the BCC and CCs will only be applied to the first address

**1.10.9**
* Added pagination on multisite settings page
* Fixed an FAQ link
* Changed a class method to protected for extensibility (user contribution)
* Added some CSS classes for subscription widget (user contribution)
* Added warning when API Key doesn't have statistics permissions
* The statistics page will not show up in menu or dashboard when API key does not have stats permissions

**1.10.8**
* Fixed an XSS vulnerability in the settings forms that would allow other admins to inject scripts

**1.10.7**
* Add port 2525 for SMTP
* Use cache for stats widget on dashboard

**1.10.6**
* Fixed logos and fonts on Stats page

**1.10.5**
* Added settings page on multisite to give access to self manage SendGrid plugin to each subsite

**1.10.4**
* Set transient token for Marketing Campaign in database

**1.10.3**
* Add option to configure text version using setText() function from the header
* Tested up to 4.7

**1.10.2**
* Add options to configure subscription widget form (labels, padding)

**1.10.1**
* Fixed a javascript error and a PHP warning

**1.10.0**
* Added basic Multisite functionality
* WARNING: Multisite users need to network activate this plugin and reconfigure it.
* Fixed an issue where other users would see the SendGrid statistics widget on the dashboard.

**1.9.5**
* Fixed an issue with the Reply-To field

**1.9.4**
* Added Unsubscribe Group option
* Improved email validation

**1.9.3**
* Added BuddyPress integration
* MC API Key is now saved on focusout
* Added posibility of using plain text template for subscription confirmation email
* Added posibility of adding shortcodes to subscription confirmation pages

**1.9.2**
* Improved response time on admin dashboard

**1.9.1**
* Added filters that allow the change of text or HTML content of all emails before they are sent
* Fixed an issue with the widget admin notice

**1.9.0**
* Added the SendGrid Subscription Widget
* The settings page now has tabs to separate the configuration of general settings from the widget settings
* Fixed an issue where a 'gzinflate()' warning was displayed in Query Monitor for each plugin request
* Fixed an issue where the API Key would be deleted from the db if it was set in wp-config

**1.8.2**
* Update SendGrid logos

**1.8.1**
* Added possibility to override the email template

**1.8.0**
* Added SendGrid\Email() for $header
* Fix Send Test form not being displayed issue

**1.7.6**
* Updated validation for email addresses in the headers field of the send test email form
* Add ability to have and individual email sent to each recipient by setting x-smtpapi-to in headers

**1.7.5**
* Fixed an issue with the reset password email from Wordpress
* Updated validation for email addresses
* Fixed an issue where some errors were not displayed on the settings page
* Add substitutions functionality

**1.7.4**
* Fixed some failing requests during API Key checks
* Fixed an error that appeared on fresh installs regarding invalid port setting

**1.7.3**
* Add global config for content-type
* Validate send_method and port set in config file
* Be able to define categories for which you would like to see your stats

**1.7.2**
* Check your credentials after updating, you might need to reenter your credentials
* Fixed mcrypt library depencency issue

**1.7.1**
* BREAKING CHANGE: Don't make update if you don't have mcrypt php library enabled
* Fixed a timeout issue from version 1.7.0

**1.7.0**
* BREAKING CHANGE : wp_mail() now returns only true/false to mirror the return values of the original wp_mail(). If you have written something custom in your function.php that depends on the old behavior of the wp_mail() you should check your code to make sure it will still work right with boolean as return value instead of array
* BREAKING CHANGE: Don't make update if you don't have mcrypt php library enabled
* Added the possibility of setting the api key or username/password empty
* Added the possibility of selecting the authentication method
* Removed dependency on cURL, now all API requests are made through Wordpress
* Sending mail via SMTP now supports API keys
* Security improvements
* Refactored old code

**1.6.9**
* Add categories in headers, add errror message on statistics page if API key is not having permissions

**1.6.8**
* Update api_key validation

**1.6.7**
* Ability to use email templates, fix category statistics, display sender test form if we only have sending errors

**1.6.6**
* Remove $plugin variable to avoid conflict with other plugins

**1.6.5**
* Add configurable port number for SMTP method, Specify full path for sendgrid php library, Fix special characters and new lines issues

**1.6.4**
* Add support for toName in API method, Add required Text Domain

**1.6.3**
* Update Smtp class name to avoid conflicts

**1.6.2**
* Add Api Keys for authentication, use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v3.2.0

**1.6.1**
* Add unique arguments

**1.6**
* Fix setTo method in SMTP option, update documentation, add link to SendGrid portal

**1.5.4**
* Updated the plugin to use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v3.0.0

**1.5.3**
* Fix attachments issue

**1.5.2**
* Fix urlencoded username issue

**1.5.1**
* Fix wp_remote issue

**1.5.0**
* Updated the plugin to use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v2.2.0

**1.4.6**
* Added constants for SendGrid settings

**1.4.5**
* Fix changelog order in readme file

**1.4.4**
* Fix unicode filename for icon-128x128.png image

**1.4.3**
* Update plugin logo, description, screenshots on installation page

**1.4.2**
* Added SendGrid Statistics for the categories added in the SendGrid Settings Page

**1.4.1**
* Added support to set additional categories

**1.4**
* Fix warnings for static method, add notice for php version < 5.3.0, refactor plugin code

**1.3.2**
* Fix URL for loading image

**1.3.1**
* Fixed reply-to to accept: "name <email@example.com>"

**1.3**
* Added support for WordPress 3.8, fixed visual issues for WordPress 3.7

**1.2.1**
* Fix errors: set_html_content_type error, WP_DEBUG enabled notice, Reply-To header is overwritten by default option

**1.2**
* Added statistics for emails sent through WordPress plugin

**1.1.3**
* Fix missing argument warning message

**1.1.2**
* Fix display for october charts

**1.1.1**
* Added default category on sending

**1.1**
* Added SendGrid Statistics

**1.0**
* Fixed issue: Add error message when PHP-curl extension is not enabled.

## Upgrade notice

**1.11.7**
* Added a configuration parameter of API request timeout in seconds
* Fixed an issue that made the HTML subscription emails break links

**1.11.6**
* Added a feature flag to disable whitespace normalization in BuddyPress plaintext emails
* Fixed an issue where the from name and email subjects would incorrectly display the ampersand symbol

**1.11.5**
* Fixed a potential stored XSS issue on the backend settings form
* Fixed a potential CSRF issue on the backend settings form

**1.11.4**
* Fixed an issue where TO field recipients could not see each other in the email header

**1.11.3**
* Fixed an issue where the send test form was displayed when no API key was set
* Fixed an issue where the subscription test form was not displayed for the default contact list
* Fixed an issue where the virtual pages for Subscription errors was not displayed
* Fixed an issue where there was no notification for option update on the Multisite settings page
* Fixed an issue where there was no notification when an API key was not set on the General tab when there was one on the Subscription Widget tab

**1.11.2**
* Relaxed PHP requirement to at least version 5.4.

**1.11.1**
* Confirmed compatibility with PHP 7 and 7.1
* Removed some legacy code that caused warnings in PHP 7
* Fixed issue where the statistics page would show up in menu even if the API key did not have stats permissions

**1.11.0**
* BREAKING CHANGE: DO NOT UPGRADE IF YOU USE PHP <= 5.3. Only PHP 5.4 and later versions are supported.
* BREAKING CHANGE: Username & Password is no longer supported. Change your settings to use an API Key before updating
* API Mail Send was changed to use the V3 SendGrid API
* Emails sent with the V2 Email Object will now be translated to V3
* BREAKING CHANGE: The date parameter on the V2 object is no longer supported
* BREAKING CHANGE: When using the V2 object with SMTPAPI Tos, the BCC and CCs will only be applied to the first address

**1.10.9**
* Added pagination on multisite settings page
* Fixed an FAQ link
* Changed a class method to protected for extensibility (user contribution)
* Added some CSS classes for subscription widget (user contribution)
* Added warning when API Key doesn't have statistics permissions
* The statistics page will not show up in menu or dashboard when API key does not have stats permissions

**1.10.8**
* Fixed an XSS vulnerability in the settings forms that would allow other admins to inject scripts

**1.10.7**
* Add port 2525 for SMTP
* Use cache for stats widget on dashboard

**1.10.6**
* Fixed logos and fonts on Stats page

**1.10.5**
* Added settings page on multisite to give access to self manage SendGrid plugin to each subsite

**1.10.4**
* Set transient token for Marketing Campaign in database

**1.10.3**
* Add option to configure text version using setText() function from the header
* Tested up to 4.7

**1.10.2**
* Add options to configure subscription widget form (labels, padding)

**1.10.1**
* Fixed a javascript error and a PHP warning

**1.10.0**
* Added basic Multisite functionality
* WARNING: Multisite users need to network activate this plugin and reconfigure it.
* Fixed an issue where other users would see the SendGrid statistics widget on the dashboard.

**1.9.5**
* Fixed an issue with the Reply-To field

**1.9.4**
* Added Unsubscribe Group option
* Improved email validation

**1.9.3**
* Added BuddyPress integration
* MC API Key is now saved on focusout
* Added posibility of using plain text template for subscription confirmation email
* Added posibility of adding shortcodes to subscription confirmation pages

**1.9.2**
* Improved response time on admin dashboard

**1.9.1**
* Added filters that allow the change of text or HTML content of all emails before they are sent
* Fixed an issue with the widget admin notice

**1.9.0**
* Added the SendGrid Subscription Widget
* The settings page now has tabs to separate the configuration of general settings from the widget settings
* Fixed an issue where a 'gzinflate()' warning was displayed in Query Monitor for each plugin request
* Fixed an issue where the API Key would be deleted from the db if it was set in wp-config

**1.8.2**
* Update SendGrid logos

**1.8.1**
* Added possibility to override the email template

**1.8.0**
* Added SendGrid\Email() for $header
* Fix Send Test form not being displayed issue

**1.7.6**
* Updated validation for email addresses in the headers field of the send test email form
* Add ability to have and individual email sent to each recipient by setting x-smtpapi-to in headers

**1.7.5**
* Fixed an issue with the reset password email from Wordpress
* Updated validation for email addresses
* Fixed an issue where some errors were not displayed on the settings page
* Add substitutions functionality

**1.7.4**
* Fixed some failing requests during API Key checks.
* Fixed an error that appeared on fresh installs regarding invalid port setting.

**1.7.3**
* Add global config for content-type
* Validate send_method and port set in config file
* Be able to define categories for which you would like to see your stats

**1.7.2**
* Check your credentials after updating, you might need to reenter your credentials
* Fixed mcrypt library depencency issue

**1.7.1**
* BREAKING CHANGE: Don't make update if you don't have mcrypt php library enabled
* Fixed a timeout issue from version 1.7.0

**1.7.0**
* BREAKING CHANGE : wp_mail() now returns only true/false to mirror the return values of the original wp_mail(). If you have written something custom in your function.php that depends on the old behavior of the wp_mail() you should check your code to make sure it will still work right with boolean as return value instead of array
* BREAKING CHANGE: Don't make update if you don't have mcrypt php library enabled
* Added the possibility of setting the api key or username/password empty
* Added the possibility of selecting the authentication method
* Removed dependency on cURL, now all API requests are made through Wordpress
* Sending mail via SMTP now supports API keys
* Security improvements
* Refactored old code

**1.6.9**
* Add categories in headers, add errror message on statistics page if API key is not having permissions

**1.6.8**
* Update api_key validation

**1.6.7**
* Ability to use email templates, fix category statistics, display sender test form if we only have sending errors

**1.6.6**
* Remove $plugin variable to avoid conflict with other plugins

**1.6.5**
* Add configurable port number for SMTP method, Specify full path for sendgrid php library, Fix special characters and new lines issues

**1.6.4**
* Add support for toName in API method, Add required Text Domain

**1.6.3**
* Update Smtp class name to avoid conflicts

**1.6.2**
* Add Api Keys for authentication, use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v3.2.0

**1.6.1**
* Add unique arguments

**1.6**
* Fix setTo method in SMTP option, update documentation, add link to SendGrid portal

**1.5.4**
* Updated the plugin to use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v3.0.0

**1.5.3**
* Fix attachments issue

**1.5.2**
* Fix urlencoded username issue

**1.5.1**
* Fix wp_remote issue

**1.5.0**
* Updated the plugin to use the last version of Sendgrid library: https://github.com/sendgrid/sendgrid-php/releases/tag/v2.2.0

**1.4.6**
* Added constants for  SendGrid settings

**1.4.5**
* Fix changelog order in readme file

**1.4.4**
* Fix unicode filename for icon-128x128.png image

**1.4.3**
* Update plugin logo, description, screenshots on installation page

**1.4.2**
* Added SendGrid Statistics for the categories added in the SendGrid Settings Page

**1.4.1**
* Added support to set additional categories

**1.4**
* Fix warnings for static method, add notice for php version < 5.3.0, refactor plugin code

**1.3**
* Added support for WordPress 3.8, fixed visual issues for WordPress 3.7

**1.2**
* Now you can switch between Sendgrid general statistics and Sendgrid WordPress statistics.

**1.1**
* SendGrid Statistics can be used by selecting the time interval for which you want to see your statistics.
