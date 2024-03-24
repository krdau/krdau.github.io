# Pico - A stupidly simple, blazing fast, flat file CMS?
Sharing my learnings on pico cms based websites (servers: lighttpd and apache2).

Since Pico CMS and the (community) plugins are not fluently updated, some Plugins do not work (properly).
Often you need to investigate in forums, etc. to find a suitable solution.

Currently the upgrade to php8.x results in numerous error warnings or complete failure.

One of my latest chalenges was the url-rewriting under lighttpd:
Pico CMS provides subpage-URLs like "/page/?subpage". To avoid that behaviour you have to change settings in  "/var/www/config.yml" (to be adopted if your pico is installed in a subdirectory):

```
…
rewrite_url: true
…
```

And in "/etc/lighttpd/lighttpd.conf" make sure to activate "mod_rewrite"

```
server.modules = (
        "mod_indexfile",
        "mod_access",
        "mod_alias",
        "mod_redirect",
        "mod_rewrite"
)
…
$HTTP["host"] == "your-hostname" {
    url.rewrite-once = (
        "^/(config|content|vendor|composer\.(json|lock|phar))(/|$)" => "/index.>
        "^/(.+/)?\.(?!well-known(/|$))" => "/index.php"
    )

    url.rewrite-if-not-file = (
        "^(/|$)" => "/index.php"
    )
}
…
```

That should do the trick for lighty.

If you are on Apache2 there is (should be) a ".htaccess" file where url-rewriting for apache2 is organized:

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    # May be required to access sub directories
    #RewriteBase /

    # Deny access to internal dirs and files by passing the URL to Pico
    RewriteRule ^(config|content|vendor|CHANGELOG\.md|composer\.(json|lock|phar>
    RewriteRule (^\.|/\.)(?!well-known(/|$)) index.php [L]

    # Enable URL rewriting
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ index.php [L]

    <IfModule mod_env.c>
        # Let Pico know about available URL rewriting
        SetEnv PICO_URL_REWRITING 1
    </IfModule>
</IfModule>
```




