# Wordpress-Security-Checklist

Go Live Security List

## 1. Protect .htaccess

```apacheconf
  <files ~ "^.*\.([Hh][Tt][Aa])">
    order allow,deny
    deny from all
    satisfy all
  </files>
```

## 2. Protect wp-config.php

```apacheconf
  <files wp-config.php>
    order allow,deny
    deny from all
  </files>
```

## 3. Protect /wp-contents/

This htaccess file needs to present inside `wp-content` folder

```apacheconf
   Order deny,allow
   Deny from all
   <Files ~ ".(xml|css|jpe?g|png|gif|js)$">
   Allow from all
   </Files>
```

## 4. Protect Include-Only files

```apacheconf
  <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^wp-admin/includes/ - [F,L]
    RewriteRule !^wp-includes/ - [S=3]
    RewriteRule ^wp-includes/[^/]+\.php$ - [F,L]
    RewriteRule ^wp-includes/js/tinymce/langs/.+\.php - [F,L]
    RewriteRule ^wp-includes/theme-compat/ - [F,L]
  </IfModule>
```

## 5. Directory Browsing

```apacheconf
  # disabling directory browsing
  Options All -Indexes
```

## 6. Disable File Edit

```php
  define('DISALLOW_FILE_EDIT',true);
```

## 7. Disabling REST API for external requests

```php
function restrict_rest_api_to_localhost() {
    $whitelist = array('127.0.0.1', "::1");

    if(!in_array($_SERVER['REMOTE_ADDR'], $whitelist)){
        die('REST API is disabled.');
    }
}
add_action( 'rest_api_init', 'restrict_rest_api_to_localhost', 1 );
```

## 8. Disabling REST API using htaccess

```apacheconf
  # WP REST API BLOCK JSON REQUESTS
  # Block/Forbid Requests to: /wp-json/wp/
  # WP REST API REQUEST METHODS: GET, POST, PUT, PATCH, DELETE
  RewriteCond %{REQUEST_METHOD} ^(GET|POST|PUT|PATCH|DELETE) [NC]
  RewriteCond %{REQUEST_URI} ^.*wp-json/wp/ [NC]
  RewriteRule ^(.*)$ - [F]
```

Pointing REST API to 404 page

```apacheconf
# WP REST API BLOCK JSON REQUESTS 
# Redirect to a 404.html (you may want to add a 404 header!) 
RewriteRule ^wp-json.*$ 404.html
```

## 9. Disable PHP Execution

In `wp-includes` and `uploads`

```apacheconf
  <Files *.php>
    Order Allow, Deny
    Deny from all
  </Files>
```

## 10. Disable XML-RPC

```apacheconf
  <Files xmlrpc.php>
    order deny,allow
    deny from all
    allow from `ip_address`
  </Files>
```

Enable this features if only updating the content using remote access

## Credits

- https://www.cloudways.com/blog/protect-wordpress-with-htaccess/
- stackoverflow