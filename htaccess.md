# .htaccess

Here is a simple cheatsheet for the .htaccess file:

## Enable Directory Browsing

	Options +Indexes
	## block a few types of files from showing
	IndexIgnore *.wmv *.mp4 *.avi

## Disable Directory Browsing

	Options All -Indexes

## Customize Error Messages

	ErrorDocument 403 /forbidden.html
	ErrorDocument 404 /notfound.html
	ErrorDocument 500 /servererror.html

## Get SSI working with HTML/SHTML

	AddType text/html .html
	AddType text/html .shtml
	AddHandler server-parsed .html
	AddHandler server-parsed .shtml
	# AddHandler server-parsed .htm

## Change Default Page (order is followed!)

	DirectoryIndex myhome.htm index.htm index.php

## Block Users from accessing the site

	<limit GET POST PUT>
	  order deny,allow
	  deny from 202.54.122.33
	  deny from 8.70.44.53
	  deny from .spammers.com
	  allow from all
	</limit>

## Allow only LAN users

	order deny,allow
	deny from all
	allow from 192.168.0.0/24

## Redirect Visitors to New Page/Directory

	Redirect oldpage.html http://www.domainname.com/newpage.html
	Redirect /olddir http://www.domainname.com/newdir/

## Block site from specific referrers

	RewriteEngine on
	RewriteCond %{HTTP_REFERER} site-to-block\.com [NC]
	RewriteCond %{HTTP_REFERER} site-to-block-2\.com [NC]
	RewriteRule .* - [F]

## Block Hot Linking/Bandwidth hogging

	RewriteEngine on
	RewriteCond %{HTTP_REFERER} !^$
	RewriteCond %{HTTP_REFERER} !^http://(www\.)?mydomain.com/.*$ [NC]
	RewriteRule \.(gif|jpg)$ - [F]

## Want to show a “Stealing is Bad” message too?

Add this below the Hot Link Blocking code:

	RewriteRule \.(gif|jpg)$ http://www.mydomain.com/dontsteal.gif [R,L]

## Stop .htaccess (or any other file) from being viewed

	<files file-name>
	  order allow,deny
	  deny from all
	</files>

## Avoid the 500 Error

	# Avoid 500 error by passing charset
	AddDefaultCharset utf-8

## Grant CGI Access in a directory

	Options +ExecCGI
	AddHandler cgi-script cgi pl
	# To enable all scripts in a directory use the following
	# SetHandler cgi-script

## Password Protecting Directories

Use the [.htaccess Password Generator](http://thejackol.com/scripts/htpasswdgen.php) and follow the brief instructions!

## Change Script Extensions

	AddType application/x-httpd-php .gne

gne will now be treated as PHP files! Similarly, x-httpd-cgi for CGI files, etc.

## Use MD5 Digests

Performance may take a hit but if thats not a problem, this is a nice option to turn on.

	ContentDigest On

## The CheckSpelling Directive

From Jens Meiert: CheckSpelling corrects simple spelling errors (for example, if someone forgets a letter or if any character is just wrong). Just add CheckSpelling On to your htaccess file.

## The ContentDigest Directive

As the Apache core features documentation says: “This directive enables the generation of Content-MD5 headers as defined in [RFC1864](http://www.ietf.org/rfc/rfc1864.txt) respectively [RFC2068](http://www.ietf.org/rfc/rfc2068.txt). The Content-MD5 header provides an end-to-end message integrity check (MIC) of the entity-body. A proxy or client may check this header for detecting accidental modification of the entity-body in transit.

Note that this can cause performance problems on your server since the message digest is computed on every request (the values are not cached). Content-MD5 is only sent for documents served by the core, and not by any module. For example, SSI documents, output from CGI scripts, and byte range responses do not have this header.”

To turn this on, just add ContentDigest On.

## Enable Gzip – Save Bandwidth

	# BEGIN GZIP
	<ifmodule mod_deflate.c>
	  # Combine the below two lines - I've split it up for presentation
	  AddOutputFilterByType DEFLATE text/text text/html text/plain text/xml text/css application/x-javascript application/javascript
	</ifmodule>
	# END GZIP
	Turn off magic_quotes_gpc

	# Only if you use PHP
	<ifmodule mod_php4.c>
	  php_flag magic_quotes_gpc off
	</ifmodule>

## Set an Expires header and enable Cache-Control

	<ifmodule mod_expires.c>
	  ExpiresActive On
	  ExpiresDefault "access plus 1 seconds"
	  ExpiresByType text/html "access plus 7200 seconds"
	  ExpiresByType image/gif "access plus 518400 seconds"
	  ExpiresByType image/jpeg "access plus 518400 seconds"
	  ExpiresByType image/png "access plus 518400 seconds"
	  ExpiresByType text/css "access plus 518400 seconds"
	  ExpiresByType text/javascript "access plus 216000 seconds"
	  ExpiresByType application/x-javascript "access plus 216000 seconds"
	</ifmodule>

	<ifmodule mod_headers.c>
	  # Cache specified files for 6 days
	  <filesmatch "\.(ico|flv|jpg|jpeg|png|gif|css|swf)$">
	  Header set Cache-Control "max-age=518400, public"
	  </filesmatch>
	  # Cache HTML files for a couple hours
	  <filesmatch "\.(html|htm)$">
	  Header set Cache-Control "max-age=7200, private, must-revalidate"
	  </filesmatch>
	  # Cache PDFs for a day
	  <filesmatch "\.(pdf)$">
	  Header set Cache-Control "max-age=86400, public"
	  </filesmatch>
	  # Cache Javascripts for 2.5 days
	  <filesmatch "\.(js)$">
	  Header set Cache-Control "max-age=216000, private"
	  </filesmatch>
	</ifmodule>
