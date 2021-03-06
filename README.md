Screens - information radiators on AirPlay devices
==================================================

Have you ever watched a movie about space missions, with the giant screens on
the wall displaying astronaut and ship status? Have you ever walked into a tech
company and seen a TV on the wall showing impressive charts and status numbers?
These are called Information Radiators! They present information into the
workspace to provide shared context about the work product and the office.

Serious information radiators might say if the system is up or down, or commute
status of nearby public transit and roadways. Fun information radiators might
report on the lunch menu. Ok, lunch can be serious, too.


Requirements
------------

* Recent Mac OS X or Linux with Avahi/Zeroconf
 * MySQL or SQLite
 * Ruby 1.9.3 or higher
 * Bundler 1.1 or higher
* A bunch of Apple TV or Raspberry Pi devices!


Quick Start
-----------

    bundle install
    bundle exec rake db:setup
    bundle exec rake assets:precompile
    bundle exec thin start
    bundle exec bin/airserver.rb --location=HERE &

Download and install either [wkhtmltopdf][] or [PhantomJS][]. See the respective
sites for instructions. If you are using PhantomJS, the [pjhtmltopdf][] script
is required as well.

A Google OAuth2 authentication method is included in the box, but is optional to use.
To use it, specify the `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` environment
variables. To authenticate users belonging to a finite set of Google Apps domains,
set the `SCREENS_VALID_EMAIL_DOMAINS` environment variable to a comma-delimited
list of accepted domains.

By default, thin runs on port 3000. You may want to run nginx as a reverse proxy
on standard HTTP or HTTPS ports.


Multiple Site Support
---------------------

Screens is divided into two components: a web interface for managing the
screens, slideshows, and slides, and the `airserver` site daemon for rendering
slides onto the target devices. Each device, e.g. an Airplay target, can be
assigned to a location name, allowing a single web view and a single database of
slides across many offices.

The `airserver` instance for each site should have its own site name provided as
a command-line argument (the `--location=HERE` argument above). The site daemon
will contact the web interface on http://localhost by default. Use option
`--server=http://yourscreens.example.com` to point the site daemon at your web
interface.

Currently, rendering takes place at each site. Either wkhtmltopdf or PhantomJS
must be installed on each site server, but not on the central web server.  This
may change in the future to centralize rendering - PRs welcome!


Screenshots
-----------
A view of the index page:

![](https://raw.github.com/wiki/brightroll/screens/media/screenshot-index.png)

Browsing the network for Airplay devices:

![](https://raw.github.com/wiki/brightroll/screens/media/screenshot-browse.png)


Power control for Sharp Aquos displays
--------------------------------------

Screens comes with a power on/off script for the IP Control port on many Sharp
Aquos displays. In this example, the script will discover all devices on
interface en3 from the arp table and attempt to telnet to port 10002. This
works well if you put the screens themselves on a separate network.

    # Turn screens on at 9:30am local time Monday - Friday
    30 9 * * 1-5   bin/aquos.rb --quiet --arp en3 --on
    # Turn screens off at 6:30pm local time every day
    30 18 * * *    bin/aquos.rb --quiet --arp en3 --off


Thanks
------

This app wouldn't be possible without the [Airplay Rubygem][] by @elcuervo.

Web page rendering uses the `wkhtmltoimage` tool from [wkhtmltopdf][] by
@antialize. Optionally [PhantomJS][] can be used instead.

An excellent alternative to Apple TV is [XBMC](http://xbmc.org)
running on a small computer like the [Raspberry Pi](http://raspberrypi.org).
I recommend the [OpenELEC.tv](http://openelec.tv) distribution.

[Airplay Rubygem]: https://github.com/elcuervo/airplay
[wkhtmltopdf]: http://github.com/wkhtmltopdf/wkhtmltopdf
[pjhtmltopdf]: http://github.com/sodabrew/pjhtmltopdf
[PhantomJS]: http://phantomjs.org
