# Website Service

Our website module provides everything you need, to manage, deploy and run your website. It is type and environment based which means you have to select a particular type (e.g. typo3cms) and environment (e.g. PROD). According those settings, our automation will setup the server/vhost as required. 


## Add website

Add a website with a configuration like this:

```
website::sites: 
  "username":
    "server_name": "example.net www.example.net"
    "env":         "PROD"
    "type":        "php"
```

 * username: Is used as system user name (SSH Login, CGI User) and database name, if a database exist
  * 2 - 16 lowercase letters only (as this name is used in several places, we have to limit its value to the least common denominator)
 * server_name: add host names which this vhost will listen on. You have to define all names explicit, also with and/or without www.
 * env: One of DEV, STAGE or PROD (see [Environments](website.md#Environments) below)
 * type: software type of this particular website (see [Types](website.md#Types) below)

By adding a website, the following parts are created on the server:

 * system user
 * system group
 * home directory (/home/username/)
 * directory for temporary files (/home/username/tmp/)
 * directory for log files (/home/username/log/)
 * directory for additional configuration files (/home/username/cnf/)
 * directory for backups (used for database dumps, /home/username/backup/)
 * environment variables for bash and zsh (~/.profile and ~/.zprofile)
 * SSH authorised keys
 * webserver vhost configuration (for custom configurations, see [Custom configurations](website.md#Custom_configurations) below


## Types

You have to define one of the following types for each website.

** Hint: ** If you need a type not mentioned here yet, do not hesitate to contact us.

** Note: ** If you configure a database here, do not forget to add the settings for the database server. See [Database configuration](database.md#Prerequisites)

#### typo3cms 

* nginx 1.6 with naxsi WAF, core rule set and TYPO3 white/blacklists
* PHP-FPM 5.6 
* MariaDB 10.x with database, user, and grants
* TYPO3 CMS 6.2 cloned into /var/lib/typo3/TYPO3_6-2/

```
website::sites: 
  "examplenet":
    "password":    "Efo9ohh4EiN3Iifeing7eijeeP4iesae"
    "server_name": "typo3.example.net www.typo3.example.net"
    "env":         "PROD"
    "type":        "typo3cms"
```

#### typo3neos 

* nginx 1.6 with naxsi WAF, core rule set and TYPO3 Neos white/blacklists
* PHP-FPM 5.6 
* MariaDB 10.x with database, user, and grants

```
website::sites: 
  "neosexample":
    "password":    "Efo9ohh4EiN3Iifeing7eijeeP4iesae"
    "server_name": "neos.example.net www.neos.example.net"
    "env":         "PROD"
    "type":        "typo3neos"
```

####  magento 

* nginx 1.6 with naxsi WAF, core rule set and magento white/blacklists
* PHP-FPM 5.6 with mcrypt module
* MariaDB 10.x with database, user, and grants

```
website::sites: 
  "magentoexample":
    "server_name": "magento.example.net"
    "env":         "PROD"
    "type":        "magento"
    "password":    "Aiw7vaakos04h7e"
```


#### wordpress

* nginx 1.6 with naxsi WAF, core rule set and wordpress white/blacklists
* PHP-FPM 5.6
* MariaDB 10.x with database, user, and grants

```
website::sites: 
  "wordpressexample":
    "server_name": "wordpress.example.net"
    "env":         "PROD"
    "type":        "wordpress"
    "password":    "Aiw7vaakos04h7e"
```

#### drupal

* nginx 1.6 with naxsi WAF, core rule set and drupal white/blacklists
* PHP-FPM 5.6 
* MariaDB 10.x with database, user, and grants

```
website::sites: 
  "drupalexample":
    "server_name": "drupal.example.net"
    "env":         "PROD"
    "type":        "drupal"
    "password":    "Aiw7vaakos04h7e"
```

#### php 

* nginx 1.6 with naxsi WAF and core rule set
* PHP-FPM 5.6 
* MariaDB 10.x with database, user, and grants (use "dbtype": "mysql", otherwise without database)

```
website::sites: 
  "phpexamplenet":
    "server_name": "php.example.net"
    "env":         "PROD"
    "type":        "php"
# uncomment the following lines if you need an automatically created database
#    "dbtype":      "mysql"
#    "password":    "Aiw7vaakos04h7e"
```


#### hhvm

* nginx 1.6 with naxsi WAF and core rule set
* HHVM with PHP-FPM 5.6 fallback
* MariaDB 10.x with database, user, and grants (use "dbtype": "mysql", otherwise without database)
* please contact us to evaluate the feasibility within your project

```
website::sites: 
  "hhvmexamplenet":
    "server_name": "hhvm.example.net"
    "env":         "PROD"
    "type":        "hhvm"
    "dbtype":      "mysql"
    "password":    "ohQueeghoh0bath"
```

#### html


* nginx 1.6 with naxsi and core rule set
* for static content only (this documentation is served trough the html type)

```
website::sites: 
  "htmlexamplenet":
    "server_name": "html.example.net"
    "env":         "PROD"
    "type":        "html"
```


#### uwsgi

* nginx 1.6 with naxsi WAF and core rule set
* uwsgi Daemon (Symlink your appropriate wsgi configuration to ~/wsgi.py)
* Python virtualenv `venv-<sitename>` configured within uwsgi and the user login shell
* there is no database added by default, choose one of
 * PostgreSQL 9.4 with database, user, and grants (`"dbtype": "postgresql"`)
 * MariaDB 10.x with database, user, and grants (`"dbtype": "mysql"`)
* all requests are redirected to the uwsgi daemon by default. To serve static files, add appropriate locations to the [local nginx configuration](website.md#Custom_configurations) like this:
```
location /static/
{
	root /home/user/application/;
}
```

```
website::sites: 
  "uwsgiexample":
    "server_name": "uwsgi.example.net"
    "env":         "PROD"
    "type":        "uwsgi"
    "dbtype":      "postgresql"
    "password":    "ohQueeghoh0bath"
```

Hint: to control the uwsgi daemon, use the `uwsgi-reload` and `uwsgi-restart` shortcuts

#### Symfony

* nginx 1.6 with naxsi WAF, core rule set and Symfony white/blacklists
* PHP-FPM 5.6 
* MariaDB 10.x with database, user, and grants

```
website::sites: 
  "symfonyexample":
    "password":    "Efo9ohh4EiN3Iifeing7eijeeP4iesae"
    "server_name": "symfony.example.net www.symfony.example.net"
    "env":         "PROD"
    "type":        "symfony"
```
Hint: For security reason, PHP execution is just allow for app.php, app_dev.php, config.php. All other requests end up in a 403 forbidden error.

## Environments

You have to select one of those environments for each website:


#### PROD

* live sites only
* no access protection
* phpinfo disabled (otherwise database credentials in environment variables could get leaked)
* quiet error log level
* E-Mails get sent to their designated recipient (PHP mail() only, see [E-Mail Handling](/development/email.md) for details)


#### STAGE 

* for stage / preview / testing access
* password protected (User "preview", password from "htpasswd" option)
* phpinfo enabled
* debug error log level
* E-Mails get saved as file into the ~/tmp/ directory (PHP mail() only, see [E-Mail Handling](/development/email.md) for details)


#### DEV

* for development
* password protected (User "preview", password from "htpasswd" option)
* phpinfo enabled
* debug error log level
* E-Mails get saved as file into the ~/tmp/ directory (PHP mail() only, see [E-Mail Handling](/development/email.md) for details)


#### User Handling

The preview user gets applied to all non PROD environments and is intended for your own use, but also to allow access to other parties like your customer. Use the "htpasswd" option to set a particular password to the preview user. You have to use a htpasswd encrypted value which you can generate like this:

```
htpasswd -n preview
```

Configuration example: 

```
"devexamplenet":
    "type":        "typo3cms"
    "env":         "DEV"
    "server_name": "dev.example.net www.dev.example.net"
    "password":    "1234"
    "htpasswd":    "$apr1$RSDdas2323$23case23DCDMY.0xgTr/"
```

Furthermore, you can add additional users trough the "website::users" configuration like this:

```
website::users:
  "alice":
    "preview": "$apr1$RXDs3l18$w0VJrVN5uoU6DMY.0xgTr/"
  "bob":
    "preview": "$apr1$RSDdas2323$23case23DCDMY.0xgTr/"
```

You can add such uers for yourself and your co-workers. If you work on multiple websites, you do not have to look up the corresponding password all the time but just use the global one.

** Note: ** Please keep in mind that this password gets often transfered over unencrypted connections. As always, we recommend to use a particular password for only this purpose.

#### Disable exeptions

Never show detailed application based exeptions on PROD, to avoid [information leakage](https://www.owasp.org/index.php/Information_Leakage). Disable the output directly in your application. For example in TYPO3:

```
$TYPO3_CONF_VARS['SYS']['displayErrors'] = '0'; 
```


### Environment Variables

For each website, the following environment variables are created by default, and are available within the shell and also the webserver.

 * SITE_ENV (DEV, STAGE or PROD)
 * DB_HOST (Database hostname, only if there is a database)
 * DB_NAME (Database name, only if there is a database)
 * DB_USERNAME (Database username, only if there is a database)
 * DB_PASSWORD (Database password, only if there is a database)


Hint: to use the .profile environmet within a cronjob, prepend the following code to your command:
/bin/bash -c 'source $HOME/.profile; ~/original/command'


#### Example usage within PHP

As soon there is a database installed, the following variables are added to the environment and can be used from within your application. TYPO3 Example:
```
$typo_db_username = $_SERVER['DB_USERNAME'];
$typo_db_password = $_SERVER['DB_PASSWORD'];
$typo_db_host     = $_SERVER['DB_HOST'];
$typo_db          = $_SERVER['DB_NAME'];
```

Additionaly, you can use the "SITE_ENV" variable to set parameters according the current environment:
```
switch ($_SERVER['SITE_ENV']) {
    case 'DEV':
        $recipient = 'dev@example.net';
        break;
    case 'STAGE':
        $recipient = 'dev@example.net';
        break;
    case 'PROD':
        $recipient = 'customer@example.com';
        break;
}
```

If you configure your application like this, you can copy all data between different servers or vhosts (DEV/STAGE/PROD) and all settings are applied as desired.


## TLS Certificates

By adding a TLS certificate to your website, the following configurations/features are applied to the vhost:

* SPDY 3.1
* TLS 1.0, 1.1, 1.2
* SNI
* HSTS
* daily Expiration Date Check
* daily [Qualys SSL Labs](https://www.ssllabs.com/) API Check
* global HTTP to HTTPS redirect


### Order certificate


#### Requirements

To validate domain ownership, our certificate issuer will send a E-Mail to one of the following addresses:

* webmaster@example.net
* admin@example.net
* administrator@example.net


#### Create certificate and key

```
$ openssl req -newkey rsa:2048 -x509 -nodes -days 3650 -out www.example.net.crt -keyout www.example.net.key
Country Name (2 letter code) [AU]:CH
State or Province Name (full name) [Some-State]:Luzern
Locality Name (eg, city) []:Luzern
Organization Name (eg, company) [Internet Widgits Pty Ltd]:example Ltd
Organizational Unit Name (eg, section) []:
Common Name (eg, YOUR name) []:www.example.net
Email Address []:webmaster@example.net
```

#### Extract certificate signing request

```
openssl x509 -x509toreq -signkey www.example.net.key -in www.example.net.crt
```

Submit this CRS to us [Support](/support.md) for further processing, or order certificate by yourself from the issuer of your choice.


#### Configure website

* ssl_key: generated private key
* ssl_cert: signed certificate, including appropriate intermediate certificates

Note: make sure to use the correct line indenting

```
website::sites: 
  "magentoexample":
    "server_name": "magento.example.net"
    "env":         "PROD"
    "type":        "magento"
    "password":    "Aiw7vaakos04h7e"
    "ssl_key": |
     -----BEGIN PRIVATE KEY-----
     MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDRHc47/0zg+cAg
     MkHKY1U7TOFChPawiNmU94MYjOTzK/Lc4C2op1sDCAP4Ow+qx7BK8NLJkHUPyOXU
     zjTTTUN/dGoElGz4gFaCCkMhk8hRZEs8jTwAm8tq4ruUVk9DIgQ9K/potm5kzT/T
     KyW85hETMLi+tRw9Kbn/j4QljWmqcd4mPWyaMT1o4lDTszq7NCHGch+dxa4FJYib
     z05C6+BVpw9w+BWFERlbgG5hvMMXtxexlju24e2fJV/TPCVbgDk/ecFDhupRMdj9
     ZKrlPcUZNReWxgX+ZGT8YfWI2tYfW9+H6iVvcwV2gftiDp4+N4r4Ae52cMFxcKBR
     5fn4i8hbAgMBAAECggEAYv66MBr3GRYChvtju9z0b2NAzE3HvuC6KFRYAlpI1Hl8
     umWCF/JKGpBD2NKU4yMvaPrCvtsdH8DaVLjdtx4/kunYepyNTcLrsRoMl6uvTCCv
     oVW3Dw6x6MK3TEzjrwM+gHr+S235qsyjp2MotVkwwiXxf46bdLT5MWuOgnyEhkQQ
     cmv6qDmjgDP5vH5r4riAlPKMq+jGtLc/2QWs22UxQS0/a7n0pks472AonLI8r1M8
     sYcCAC6uEvxRZdVcJOlRK78dPI3NLxFhSbvv/GcVOypyhvQ3uVYV71xA/AgcpBLd
     Fc2FULRCCU/UEjmo62uYNkG09lCchBwK8BLYYWrCoQKBgQDqL5Eo9oLMTuzysu8I
     vemXODOTfxQb1OTH1dyA4kSAtmNF2IO5rNnvVsS5YlbSjZMEXRMYTSflp7L7ae2l
     XLqjhijdB6l5cdgsPyHD2phYOvJzWMuzjkCtIjm5QfdMfsUZnBSPbwaRF1zWxbVn
     mHlWi3Zcu8U65l9gsJviZelqqwKBgQDkmG4W1SEySON4i44JwzsmXQHP1d8KHES1
     hB1IETNYV2HzIAWnnX/fqPwqyahzegKTGut9U7kJ8QHsHvz56nHdiQ8zw4BZxQPw
     j4Pms1IpzpO48yf4swskqwgkk5W6wTHCD/Q48tqFtAMPwC/D88F966ipc6lyldsJ
     UXvLeeMZEQKBgGTHYZWaOAGKOYfcHufJKnwMEI350wKDJI0m6ISCWu51DtWg7lb6
     HrNTyMbqnehwSoNHNo9vrKq0914gYMeX1y3F71HnGTSNHHU2Gea57HOTsoCXBtpX
     blfTcbnavHyr1VBHDcYIBnBr+GTooj9Zq2XmEGKp35+QQh1PA1ZzevaPAoGASdop
     Lv06VVmRC9/iSqslT/aaYEATZ9vMIuyE3USZVwAcKAT/brCGoIaiuVwfLPeNH2OC
     EyJaVKjlWxiD2GXy1YSzQaD2tYneBPkIvx7N+62+sfD0x/doMTeEUPTRWd2SqsSm
     vUNQcAPBPXR0uhTlPT5GZkB0zQ03D6KgoRNG2FECgYEAgXPJjIsqhcC0PNEuRgdC
     9pZq+Prvp4TniVwQKyPniw/FjAplI4uN/+1fiYPexaLzINnXUuvOTYPABec3T2DZ
     GV0lffmdZ+CleU1Xi5DjLGn8m0Gdy6mecE2Le9/Q13o3owF9rm0Drhqqd8T6vVt3
     hiw7C+lCp2XheqP+QchwxiY=
     -----END PRIVATE KEY-----
    "ssl_cert": |
     -----BEGIN CERTIFICATE-----
     MIIEATCCAumgAwIBAgIJAMdVCMOVZl30MA0GCSqGSIb3DQEBCwUAMIGWMQswCQYD
     VQQGEwJDSDEPMA0GA1UECAwGWnVyaWNoMQ8wDQYDVQQHDAZadXJpY2gxIzAhBgNV
     BAoMGnNub3dmbGFrZSBwcm9kdWN0aW9ucyBHbWJIMRowGAYDVQQDDBF0eXBvMy5l
     eGFtcGxlLm5ldDEkMCIGCSqGSIb3DQEJARYVd2VibWFzdGVyQGV4YW1wbGUubmV0
     MB4XDTE1MDIxMjEyMDU1MloXDTI1MDIwOTEyMDU1MlowgZYxCzAJBgNVBAYTAkNI
     MQ8wDQYDVQQIDAZadXJpY2gxDzANBgNVBAcMBlp1cmljaDEjMCEGA1UECgwac25v
     d2ZsYWtlIHByb2R1Y3Rpb25zIEdtYkgxGjAYBgNVBAMMEXR5cG8zLmV4YW1wbGUu
     bmV0MSQwIgYJKoZIhvcNAQkBFhV3ZWJtYXN0ZXJAZXhhbXBsZS5uZXQwggEiMA0G
     CSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDRHc47/0zg+cAgMkHKY1U7TOFChPaw
     iNmU94MYjOTzK/Lc4C2op1sDCAP4Ow+qx7BK8NLJkHUPyOXUzjTTTUN/dGoElGz4
     gFaCCkMhk8hRZEs8jTwAm8tq4ruUVk9DIgQ9K/potm5kzT/TKyW85hETMLi+tRw9
     Kbn/j4QljWmqcd4mPWyaMT1o4lDTszq7NCHGch+dxa4FJYibz05C6+BVpw9w+BWF
     ERlbgG5hvMMXtxexlju24e2fJV/TPCVbgDk/ecFDhupRMdj9ZKrlPcUZNReWxgX+
     ZGT8YfWI2tYfW9+H6iVvcwV2gftiDp4+N4r4Ae52cMFxcKBR5fn4i8hbAgMBAAGj
     UDBOMB0GA1UdDgQWBBSSJyPyLa8CNKMDR3BAOcuuzzEqlTAfBgNVHSMEGDAWgBSS
     JyPyLa8CNKMDR3BAOcuuzzEqlTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBCwUA
     A4IBAQAMKv2Kdw2LkskJm/GAkEsavoYf6qAPruwcsp8cx+7doXOpptZ/w+m8NK8i
     6ffi65wQ4TGlFxEvXM1Ts4S1xF/+6JVnnp8RVGvfgDL7xi6juMqbtM5yBxjHKO6W
     AuxOmwPcd6cO5qL+MCSgIe13bn/V4bw/JLv9LONuwXHJuv0FEoazbSyB6uTwYf2D
     pWHEkXvkz5A1hqu3y2jFq2cQffoO31GGx29U3uSl+Esp5bL/J0bQd3TUbwvu6FY1
     NgUR7Mx1t/4/uk9FRl87d2rRslc5VyvD5v7MFE6jYJap74j5BrrfUUTNbzVXdPCS
     v8jOaIjDp5AMoZxbPMlv/5Tk85uF
```

#### Test

We recommend the following online services for testing:

* [Qualys SSL Labs](https://www.ssllabs.com/ssltest/)
* [Symantec SSL Toolbox](https://ssltools.websecurity.symantec.com/checker/views/certCheck.jsp)


## Web Application Firewall

We use [Naxsi](https://github.com/nbs-system/naxsi) as additional protection against application level attacks such as cross site-scripting or SQL injections. We also block common vulnerabilities and zero day attacks, see our [status site](http://status.snowflake.ch/) for updates.

Warning: this is just a additional security measure. Regardless its existence, remember to keep your application, extensions and libraries secure and up to date


### Identify blocks

If a request is blocked, the server will issue a "403 forbidden" error. There are detailed informations available in the error log file:

```
2015/02/17 14:03:04 [error] 15296#0: *1855 NAXSI_FMT: ip=192.168.0.22&server=www.example.net&uri=/admin/&learning=0&vers=0.53-1&total_processed=1&total_blocked=1&block=1&cscore0=$XSS&score0=8&zone0=BODY|NAME&id0=1310&var_name0=login[username]&zone1=BODY|NAME&id1=1311&var_name1=login[username], client: 192.168.0.22, server: www.example.net, request: "POST /admin/ HTTP/1.1", host: "www.example.net", referrer: "http://www.example.net/admin/"
```

To learn more about the log syntax, vist the [Naxsi documentation](https://github.com/nbs-system/naxsi/wiki).


#### Extensiv logging

If you want to debug the WAF block (often used with internal rules), you can increase the nginx error log level to "debug".

See [Nginx documentation error log](http://nginx.org/en/docs/ngx_core_module.html#error_log) for more information.


### Manage false positives

If you are certain, that your request to the application is valid (and well coded), you can whitelist the affected rule(s) within the ~/cnf/nginx_waf.conf File:

```
BasicRule wl:1310,1311 "mz:$ARGS_VAR:tx_sfpevents_sfpevents[event]|NAME";
BasicRule wl:1310,1311 "mz:$ARGS_VAR:tx_sfpevents_sfpevents[controller]|NAME";
```

See the [Naxsi documentation](https://github.com/nbs-system/naxsi/wiki/whitelists) for details.

Hint: to apply the changes reload the nginx configuration with the "nginx-reload" shortcut

Hint: if you need assistance, do not hesitate to [contact us](/support.md). We are happy to help out!

Hint: we strongly recommend to add the ~/cnf/ directory to the source code management of your choice


### Autocreate rules

With nx_util, you can parse & analyze naxsi log files. It will propose appropriate whitelist rules tailored to your application

Warning: Use on DEV/STAGE Environment only. Otherwise you will end up whitelisting actual attacks.

```
/usr/local/bin/nx_util.py -lo error.log

Deleting old database :naxsi_sig
List of imported files :['error.log']
Importing file error.log
        Successful events :6
        Filtered out events :0
        Non-naxsi lines :0
        Malformed/incomplete lines 5
End of db commit...
Count (lines) success:6
########### Optimized Rules Suggestion ##################
# total_count:2 (33.33%), peer_count:1 (100.0%) | ], possible js
BasicRule wl:1311 "mz:$URL:/events/event/|$ARGS_VAR:tx_sfpevents_sfpevents[controller]|NAME";
# total_count:2 (33.33%), peer_count:1 (100.0%) | [, possible js
BasicRule wl:1310 "mz:$URL:/events/event/|$ARGS_VAR:tx_sfpevents_sfpevents[controller]|NAME";
# total_count:1 (16.67%), peer_count:1 (100.0%) | ], possible js
BasicRule wl:1311 "mz:$URL:/events/event/|$ARGS_VAR:tx_sfpevents_sfpevents[event]|NAME";
# total_count:1 (16.67%), peer_count:1 (100.0%) | [, possible js
BasicRule wl:1310 "mz:$URL:/events/event/|$ARGS_VAR:tx_sfpevents_sfpevents[event]|NAME";

```

### Learning Mode

To enable the Naxsi learning mode, set the Naxsi flag in the `~/cnf/nginx.conf` file:

```
 set $naxsi_flag_learning 1;
```

Which means that Naxsi will not block any request, but logs the "to-be-blocked" requests in your `~log/error.log`.

Warning: Use on DEV/STAGE Enviroment only. Otherwise you will end up with an unprotected installation.

Make sure, that you analyze the error.log carefully and only whitelist valid requests afterwards.


#### Dynamic configuration

Naxsi supports a limited set of variables, that can override or modify its behavoir.
You can use them in your `~/cnf/nginx.conf` file. For example, enable the learning mode for an specific ip:

```
 if ($remote_addr = "1.2.3.4") {
  set $naxsi_flag_learning 1;
 }
```
More on the [dynamicmodifiers page](https://github.com/nbs-system/naxsi/wiki/dynamicmodifiers).

Hint: this is a powerful feature in use with the [nginx vars](http://nginx.org/en/docs/varindex.html) 


## Request limits

The number of connections and requests are limited to ensure that a single user (or bot) cannot overload the whole server.

### Limits

* 50 connections / address 
* 50 requests / second / address
* 150 requests / second (burst)
* \>150 requests / second / address (access limited)

With this configuration, a particular visitor can open up to 50 concurrent connections and issue up to 50 requests / second.

If the visitor issues more than 50 request / second, those requests are delayed and other clients are served first.

If the visitor issues more than 150 request / second, those requests will not processed anymore, but answered with the 503 status code.


### Adjust limits 

To adjust this limits (e.g. for special applications such as API calls, etc), set a higher "load zone" in your local configuration (~/cnf/nginx.conf):

```
# connection limits (e.g. 75 connections)
limit_conn addr 75;

# limit requests / second: (small, medium, large)
limit_req zone=medium burst=500;
limit_req zone=large burst=1500;
```

Hint: to apply the changes reload the nginx configuration with the "nginx-reload" shortcut


### Zones

* small = 50 requests / second (burst: 150req/sec)
* medium = 150 requests / second (burst: 500 req/sec)
* large = 500 requests / second (burst: 1500 req/sec)

Note: the default zone is "small" and will fit most use cases

Warning: In SPDY, each concurrent request is considered a separate connection.

Hint: To add more specific rules (based on client addresses or url for example), please get in touch

Hint: For Details, see the [Module ngx_http_limit_req_module](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html) documentation

 
## Custom configuration

You can add specific configurations like redirects or headers within the ~/cnf/ directory.

** Warning: ** You have to reload nginx after changes with the "nginx-reload" shortcut


### ~/cnf/nginx.conf

Included within the server block and used to configure specific redirects, enable gzip and other stuff directly in the nginx.conf.

```
if ($http_host = www.example.net) {
	rewrite (.*) http://www.example.com;
}
```

or you can password protect a subdirectory:

```
location ~* "^/example/" {
	auth_basic "Example name";
	auth_basic_user_file /home/user/www/example/.htpasswd;
	root /home/user/www/;
}
```
if you like to run PHP in this subdirectory, don't forget to add this nested in the location section from the example on top:

```
location ~ \.php {
	try_files /dummy/$uri @php;
}

```

Hint: For Details, see the [Server Block Examples](http://wiki.nginx.org/ServerBlockExample) and [Rewrite Rule](http://wiki.nginx.org/HttpRewriteModule#rewrite) documentation


### ~/cnf/nginx_waf.conf

Configure WAF exeptions here, see [Web Application Firewall](#Web_Application_Firewall) for details.


## GeoIP

To use your GeoIP database with nginx, store the appropriate data files on your server and add the following configuration:

```
# GeoIP Settings for nginx
nginx::http_cfg_append:
  - "geoip_country	/home/user/geoip/GeoIPv6.dat"
  - "geoip_city	/home/user/geoip/GeoLiteCityv6.dat"

# GeoIP related environment variables
environment::variables:
  "GEOIP_ADDR":         "$remote_addr"
  "GEOIP_ADDR":         "$remote_addr"
  "GEOIP_COUNTRY_CODE": "$geoip_country_code"
  "GEOIP_COUNTRY_NAME": "$geoip_country_name"
  "GEOIP_REGION":       "$geoip_region"
  "GEOIP_REGION_NAME":  "$geoip_region_name"
  "GEOIP_CITY":         "$geoip_city"
  "GEOIP_AREA_CODE":    "$geoip_area_code"
  "GEOIP_LATITUDE":     "$geoip_latitude"
  "GEOIP_LONGITUDE":    "$geoip_longitude"
  "GEOIP_POSTAL_CODE":  "$geoip_postal_code"
```

Hint: For details, see the [Module ngx_http_geoip_module](http://nginx.org/en/docs/http/ngx_http_geoip_module.html) documentation. 

## PHP Modules

To install additional PHP modules, use the following configuration:

```
website::php:
  "modulename":
    "ensure": "installed"
    "package": "php5-packagename"
```


For example if you like to install php5-ldap use:

```
website::php:
  "ldap":
    "ensure": "installed"
    "package": "php5-ldap"
```

Hint: Some types might have the needed package preinstalled. For example "magento" comes with "php5-mcrypt".

You will find a list of supported PHP Modules [Here](http://puppet-php.readthedocs.org/en/latest/extensions.html).

## Composer

Every PHP based website type has composer installed and auto updated.

Hint: For details, see the [Composer](https://getcomposer.org/doc/) documentation.

#### TYPO3 CMS with Composer

To use TYPO3 CMS 6.x or 7.x with composer, use the following command:

```
# Export HTTP PROXY settings to use with get.typo3.org
export HTTP_PROXY_REQUEST_FULLURI=false

# Download the Base Distribution, the latest "stable" release (6.2)
composer create-project typo3/cms-base-distribution CmsBaseDistribution

# Download the Base Distribution, the "dev" branch (7.x)
composer create-project typo3/cms-base-distribution CmsBaseDistribution dev-master

# Download the Base Distribution, the "dev" 6.2 branch
composer create-project typo3/cms-base-distribution CmsBaseDistribution 6.2.x-dev
```

#### TYPO3 Neos with Composer

To use TYPO3 Neos 1.2 with composer, use the following command:

```
# Create Web/tmp directory, install Neos 1.2 with composer, move to users home directory and cleanup
mkdir ~/Web/tmp/ && cd ~/Web/tmp/ && composer create-project --no-dev typo3/neos-base-distribution TYPO3-Neos-1.2 && rsync -a --delete-after ~/Web/tmp/TYPO3-Neos-1.2/ ~/
```

#### Symfony with Composer

To use Symfony 2 with composer, use the following command:

```
# Create Web/tmp directory, install Symfony2 with composer, move to users home directory and cleanup
mkdir ~/web/tmp/ && cd ~/web/tmp/ && composer create-project symfony/framework-standard-edition symfony && rsync -a --delete-after ~/web/tmp/symfony/ ~/
```

## Delete website

As a security measure, you have to define explicit that you want to delete a website:

```
website::sites: 
  "examplenet":
    "ensure": "absent"
```

As soon as "ensure" equals set to "absent", all configurations and data related to this site gets removed at once. After the next configuration run, you can remove the whole part from the website::sites hash.

Warning: After setting `ensure` to `absent`, do not run `puppet-agent` with this particular user. Use another, remaining user or the generic `devop` user to run `puppet-agent`


## Full configuration example

```
website::sites: 
  "examplenet":
    "password":    "1234"
    "server_name": "typo3.example.net www.typo3.example.net"
    "env":         "PROD"
    "type":        "typo3cms"
  "devexamplenet":
    "password":    "1234"
    "server_name": "dev.example.net www.dev.example.net"
    "env":         "DEV"
    "htpasswd":    "$apr1$RSDdas2323$23case23DCDMY.0xgTr/"
    "type":        "typo3cms"
  "wordpressexample":
    "server_name": "wordpress.example.net"
    "env":         "PROD"
    "type":        "wordpress"
    "password":    "Aiw7vaakos04h7e"
  "drupalexample":
    "server_name": "drupal.example.net"
    "env":         "PROD"
    "type":        "drupal"
    "password":    "Aiw7vaakos04h7e"
  "phpexamplenet":
    "server_name": "php.example.net"
    "env":         "PROD"
    "type":        "php"
  "hhvmexamplenet":
    "server_name": "hhvm.example.net"
    "env":         "PROD"
    "type":        "php"
    "dbtype":      "mysql"
    "password":    "ohQueeghoh0bath"
  "htmlexamplenet":
    "server_name": "html.example.net"
    "env":         "PROD"
    "type":        "html"
  "neosexample":
    "password":    "Efo9ohh4EiN3Iifeing7eijeeP4iesae"
    "server_name": "neos.example.net www.neos.example.net"
    "env":         "PROD"
    "type":        "typo3neos"
  "uwsgiexample":
    "server_name": "uwsgi.example.net"
    "env":         "PROD"
    "type":        "uwsgi"
    "dbtype":      "postgresql"
    "password":    "ohQueeghoh0bath"
  "symfonyexample":
    "server_name": "symfony.example.net www.symfony.example.net"
    "password":    "Efo9ohh4EiN3Iifeing7eijeeP4iesae"
    "env":         "PROD"
    "type":        "symfony"
  "magentoexample":
    "server_name": "magento.example.net"
    "env":         "PROD"
    "type":        "magento"
    "password":    "Aiw7vaakos04h7e"
    "ssl_key": |
     -----BEGIN PRIVATE KEY-----
     MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDRHc47/0zg+cAg
     MkHKY1U7TOFChPawiNmU94MYjOTzK/Lc4C2op1sDCAP4Ow+qx7BK8NLJkHUPyOXU
     zjTTTUN/dGoElGz4gFaCCkMhk8hRZEs8jTwAm8tq4ruUVk9DIgQ9K/potm5kzT/T
     KyW85hETMLi+tRw9Kbn/j4QljWmqcd4mPWyaMT1o4lDTszq7NCHGch+dxa4FJYib
     z05C6+BVpw9w+BWFERlbgG5hvMMXtxexlju24e2fJV/TPCVbgDk/ecFDhupRMdj9
     ZKrlPcUZNReWxgX+ZGT8YfWI2tYfW9+H6iVvcwV2gftiDp4+N4r4Ae52cMFxcKBR
     5fn4i8hbAgMBAAECggEAYv66MBr3GRYChvtju9z0b2NAzE3HvuC6KFRYAlpI1Hl8
     umWCF/JKGpBD2NKU4yMvaPrCvtsdH8DaVLjdtx4/kunYepyNTcLrsRoMl6uvTCCv
     oVW3Dw6x6MK3TEzjrwM+gHr+S235qsyjp2MotVkwwiXxf46bdLT5MWuOgnyEhkQQ
     cmv6qDmjgDP5vH5r4riAlPKMq+jGtLc/2QWs22UxQS0/a7n0pks472AonLI8r1M8
     sYcCAC6uEvxRZdVcJOlRK78dPI3NLxFhSbvv/GcVOypyhvQ3uVYV71xA/AgcpBLd
     Fc2FULRCCU/UEjmo62uYNkG09lCchBwK8BLYYWrCoQKBgQDqL5Eo9oLMTuzysu8I
     vemXODOTfxQb1OTH1dyA4kSAtmNF2IO5rNnvVsS5YlbSjZMEXRMYTSflp7L7ae2l
     XLqjhijdB6l5cdgsPyHD2phYOvJzWMuzjkCtIjm5QfdMfsUZnBSPbwaRF1zWxbVn
     mHlWi3Zcu8U65l9gsJviZelqqwKBgQDkmG4W1SEySON4i44JwzsmXQHP1d8KHES1
     hB1IETNYV2HzIAWnnX/fqPwqyahzegKTGut9U7kJ8QHsHvz56nHdiQ8zw4BZxQPw
     j4Pms1IpzpO48yf4swskqwgkk5W6wTHCD/Q48tqFtAMPwC/D88F966ipc6lyldsJ
     UXvLeeMZEQKBgGTHYZWaOAGKOYfcHufJKnwMEI350wKDJI0m6ISCWu51DtWg7lb6
     HrNTyMbqnehwSoNHNo9vrKq0914gYMeX1y3F71HnGTSNHHU2Gea57HOTsoCXBtpX
     blfTcbnavHyr1VBHDcYIBnBr+GTooj9Zq2XmEGKp35+QQh1PA1ZzevaPAoGASdop
     Lv06VVmRC9/iSqslT/aaYEATZ9vMIuyE3USZVwAcKAT/brCGoIaiuVwfLPeNH2OC
     EyJaVKjlWxiD2GXy1YSzQaD2tYneBPkIvx7N+62+sfD0x/doMTeEUPTRWd2SqsSm
     vUNQcAPBPXR0uhTlPT5GZkB0zQ03D6KgoRNG2FECgYEAgXPJjIsqhcC0PNEuRgdC
     9pZq+Prvp4TniVwQKyPniw/FjAplI4uN/+1fiYPexaLzINnXUuvOTYPABec3T2DZ
     GV0lffmdZ+CleU1Xi5DjLGn8m0Gdy6mecE2Le9/Q13o3owF9rm0Drhqqd8T6vVt3
     hiw7C+lCp2XheqP+QchwxiY=
     -----END PRIVATE KEY-----
    "ssl_cert": |
     -----BEGIN CERTIFICATE-----
     MIIEATCCAumgAwIBAgIJAMdVCMOVZl30MA0GCSqGSIb3DQEBCwUAMIGWMQswCQYD
     VQQGEwJDSDEPMA0GA1UECAwGWnVyaWNoMQ8wDQYDVQQHDAZadXJpY2gxIzAhBgNV
     BAoMGnNub3dmbGFrZSBwcm9kdWN0aW9ucyBHbWJIMRowGAYDVQQDDBF0eXBvMy5l
     eGFtcGxlLm5ldDEkMCIGCSqGSIb3DQEJARYVd2VibWFzdGVyQGV4YW1wbGUubmV0
     MB4XDTE1MDIxMjEyMDU1MloXDTI1MDIwOTEyMDU1MlowgZYxCzAJBgNVBAYTAkNI
     MQ8wDQYDVQQIDAZadXJpY2gxDzANBgNVBAcMBlp1cmljaDEjMCEGA1UECgwac25v
     d2ZsYWtlIHByb2R1Y3Rpb25zIEdtYkgxGjAYBgNVBAMMEXR5cG8zLmV4YW1wbGUu
     bmV0MSQwIgYJKoZIhvcNAQkBFhV3ZWJtYXN0ZXJAZXhhbXBsZS5uZXQwggEiMA0G
     CSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDRHc47/0zg+cAgMkHKY1U7TOFChPaw
     iNmU94MYjOTzK/Lc4C2op1sDCAP4Ow+qx7BK8NLJkHUPyOXUzjTTTUN/dGoElGz4
     gFaCCkMhk8hRZEs8jTwAm8tq4ruUVk9DIgQ9K/potm5kzT/TKyW85hETMLi+tRw9
     Kbn/j4QljWmqcd4mPWyaMT1o4lDTszq7NCHGch+dxa4FJYibz05C6+BVpw9w+BWF
     ERlbgG5hvMMXtxexlju24e2fJV/TPCVbgDk/ecFDhupRMdj9ZKrlPcUZNReWxgX+
     ZGT8YfWI2tYfW9+H6iVvcwV2gftiDp4+N4r4Ae52cMFxcKBR5fn4i8hbAgMBAAGj
     UDBOMB0GA1UdDgQWBBSSJyPyLa8CNKMDR3BAOcuuzzEqlTAfBgNVHSMEGDAWgBSS
     JyPyLa8CNKMDR3BAOcuuzzEqlTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBCwUA
     A4IBAQAMKv2Kdw2LkskJm/GAkEsavoYf6qAPruwcsp8cx+7doXOpptZ/w+m8NK8i
     6ffi65wQ4TGlFxEvXM1Ts4S1xF/+6JVnnp8RVGvfgDL7xi6juMqbtM5yBxjHKO6W
     AuxOmwPcd6cO5qL+MCSgIe13bn/V4bw/JLv9LONuwXHJuv0FEoazbSyB6uTwYf2D
     pWHEkXvkz5A1hqu3y2jFq2cQffoO31GGx29U3uSl+Esp5bL/J0bQd3TUbwvu6FY1
     NgUR7Mx1t/4/uk9FRl87d2rRslc5VyvD5v7MFE6jYJap74j5BrrfUUTNbzVXdPCS
     v8jOaIjDp5AMoZxbPMlv/5Tk85uF
     -----END CERTIFICATE-----
  "deleteme":
     "ensure": "absent"
```

