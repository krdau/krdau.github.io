# Pico - A stupidly simple, blazing fast, flat file CMS
Sharing my learnings on pico cms based websites (servers: lighttpd and apache2).

Since Pico CMS and the (community) plugins are not fluently updated, some Plugins do not work (properly).
Often you need to investigate in forums, etc. to find a suitable solution.

Currently the upgrade to php8.x results in numerous error warnings or complete failure.

One of my latest chalenges was the url-rewriting under lighttpd:
Pico CMS provides subpage-URLs like "/page/?subpage". To avoid that behaviour you have to change settings in  "/var/www/config.yml" (to be adopted if your pico is installed in a subdirectory):

`
…
rewrite_url: true
…
`
An in "/etc/lighttpd/lighttpd.conf" make sure to activate "mod_rewrite"

`
server.modules = (
        "mod_indexfile",
        "mod_access",
        "mod_alias",
        "mod_redirect",
        "mod_rewrite"
)
`
That should do the trick.

