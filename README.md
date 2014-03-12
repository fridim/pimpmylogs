pimpmylog
==========

minimal web UI for IRC logs

![pimpmylog](http://i.imgur.com/kwabIOD.png)

[Live demo](http://logs.onfi.re/racket/)

Overview
--------

This is a simple viewer for IRC log. I'm coding my own, because:

* I want a minimalist UI, simple and clean look
* I want it to be simple but functional
* I want it to be written in [Racket](http://racket-lang.org)

Usage :

    raco pimpmylog -h
    pimpmylog [ <option> ... ] <filename>
     where <option> is one of
    / -w, --weechat : input file is in weechat log format
    \ -r, --racket-org : input file is in format as files on http://racket-lang.org/irc-logs/racket/
      -n <cn>, --name <cn> : Name of the channel
      -p <p>, --port <p> : Listening port
      --help, -h : Show this help
      -- : Do not treat any remaining argument as a switch (at this level)
     /|\ Brackets indicate mutually exclusive options.
     Multiple single-letter switches can be combined after one `-'; for
      example: `-h-' is the same as `-h --'

Example :

    raco pimpmylog -r -n '#racket' logs/racket-logs/racket.log

### Features

* handy navigation (day, week, month, ...)
* stupid search (a grep -i)
* a specific line in history can be shared (simply copy/paste the link to that line)
* works fine with proxy cache (tested with [varnish](https://www.varnish-cache.org/) default conf) as it sets Cache-Control HTTP header
* easy interface to support other log formats. Supported log formats :
  * #racket@freenode [archives](http://racket-lang.org/irc-logs/racket/)
  * weechat
* HTML5

Quick Start
-----------
### Install
1. Install [Racket](http://racket-lang.org)
2. Install pimpmylog package :
   <pre>raco pkg install --deps search-auto git://github.com/fridim/pimpmylog</pre>
3. Start using it
   <pre>raco pimpmylog -h</pre>

#### Or build from the sources
You can produce a standalone binary :

    git clone https://github.com/fridim/pimpmylog.git
    cd pimpmylog
    raco exe main.rkt -o pimpmylog
    ./pimpmylog -h

### Easy update
Prerequisite : you installed pimpmylog via <code>raco</code>.

    raco pkg update pimpmylog

This will fetch the latest version from github.

### Run the demo

    ./tools/fetch_racket-lang.org.sh
    raco pimpmylog -r -n '#racket' logs/racket.log

## Configure a proxy

### Varnish

The default Varnish configuration will work ; you just have to set the right host/port for the pimpmylog instance :

    backend default {
         .host = "127.0.0.1";
         .port = "8000";
    }

Pimpmylog sets a Cache-Control header, so caching with Varnish should work well out of the box.

#### Cache-Control header

> The Cache-Control general-header field is used to specify directives that MUST be obeyed by all caching mechanisms along the request/response chain. The directives specify behavior intended to prevent caches from adversely interfering with the request or response. These directives typically override the default caching algorithms. Cache directives are unidirectional in that the presence of a directive in a request does not imply that the same directive is to be given in the response.

In pimpmylog the Cache-Control max-age value depends on the page visited:

<table>
<tr><th>Page</th><th>time cached</th></tr>
<tr><td>last day</td><td>1 minute</td></tr>
<tr><td>search results</td><td>1 day</td></tr>
<tr><td>everything in the past (won't change)</td><td>forever</td></tr>
</table>

### Nginx

    location / {
      proxy_pass        http://localhost:8000;
    }

Nginx can act as a proxy cache, please refer to the [documentation](http://wiki.nginx.org/HttpProxyModule). Here is an example :

    http {
      proxy_cache_path  /data/nginx/cache  levels=1:2   keys_zone=pimpmylog:100m
                        inactive=100d      max_size=1g;

      server {
        listen       80;
        server_name  localhost;

        location / {
          proxy_pass        http://localhost:8000;
          proxy_cache       pimpmylog;
          add_header X-Cache $upstream_cache_status;
        }
      }
    }

Tests
-----

    raco test .

See [Rackunit](http://docs.racket-lang.org/rackunit/).

Contrib?
--------

* TODO: add a checkbox to enable RAW format
* TODO: add irssi log format
* TODO: add a converter interface to translate from one format to another
* TODO: support mobile/pad
* TODO: add a calendar to go to a specific day/week/month
* TODO: add current topic next to title
* TODO: enable multi-word search
* TODO: speed: create a map date/fileposition and use file-position
* IDEA: support multiple files ?
* IDEA: add a timeline

### Done
* <del>make it a command line tool</del>
* <del>add structure for a msg</del>
* <del>a new log-format support should be as simple as a procedure string -> struct msg</del>
* <del>add structure for a msg</del>
* <del>find a way to put command-line in module main (in a clean way) AKA fix tests</del>
* <del>add color for nicknames</del>
* <del>add listening port as option</del>
* <del>log format definitions should be in a separate forlder</del>
* <del>highlight searched word(s) in search result</del>
* <del>make « 1 day » the homepage</del>
* <del>make the navigation bar sticky</del>
* <del>homepage: jump to bottom (lastlog)</del>
* <del>HTML5</del>
* <del>validate HTML and CSS</del>
* <del>make search case-insensitive</del>
* <del>fix color for date and nickname when row is highlighted</del>
* <del>add ajax fetch on scrolling (faster)</del>
* <del>create a package and use raco to install pimpmylog binary</del>
* <del>use lang rackjure</del>
