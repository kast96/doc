---
sidebar_position: 2
---

# Htaccess редиректы

```apacheconf
#remove .html
RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.html?\ HTTP/
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)index\.html?$ https://%1/$1 [R=301,L]

RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.html?\ HTTP/
RewriteRule ^(.*)index\.html?$ https://%{HTTP_HOST}/$1 [R=301,L]

#remove .htm
RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.htm?\ HTTP/
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)index\.htm?$ https://%1/$1 [R=301,L]

RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.htm?\ HTTP/
RewriteRule ^(.*)index\.htm?$ https://%{HTTP_HOST}/$1 [R=301,L]

#remove .php
RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.php?\ HTTP/
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)index\.php?$ https://%1/$1 [R=301,L]

RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /.*index\.php?\ HTTP/
RewriteRule ^(.*)index\.phps?$ https://%{HTTP_HOST}/$1 [R=301,L]

#from www to not www
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ https://%1/$1 [L,R=301]

#upper words to lower
RewriteCond %{REQUEST_URI} !^/(bitrix|local|ajax|include) [NC]
RewriteCond expr "tolower(%{REQUEST_URI}) =~ /(.*)/"
RewriteRule [A-Z] %1 [R=301,L]

#without slash to with slash
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteRule ^(.*[^\/])$ https://%{HTTP_HOST}/$1/ [R=301,L]

#to https variant1
#RewriteCond %{HTTPS} !=on
#RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]

#to https variant2
#RewriteCond %{SERVER_PORT} !^443$
#RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [R,L]

#to https variant3
RewriteCond %{HTTPS} off
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

#remove double slash
RewriteCond %{THE_REQUEST} //
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```