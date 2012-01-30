# versionator - Static content versioning middleware for connect and express.

[![build status](https://secure.travis-ci.org/serby/versionator.png)](http://travis-ci.org/serby/versionator)

versionator was built to solve the problem of static assets getting stuck in browser and proxy caches when new versions of the assets are deployed.

Without versionator this is what can happen:

You set your static content to be cached and expire in 30 days time. 

     express.static(__dirname + '/public', { maxAge: 2592000000 })

This gives you more capacity on your web servers and a better rating on Google Pagespeed and ySlow.

You deploy your site and all is good.

Then you need to make a change to sprite.png or app.js

You make your changes and redeploy. The trouble now is that everyone who has looked at your site already has the old version in their browser cache. Not only that, any upstream proxies will also have a copy.

A possible solution is to rename your static assets every time you change them, but that is impractical as you also have to update all the references each time they change. If you have a single CSS sprite then this is a real pain.

A better solution is to use versionator!

## Installation

      npm install versionator

## Usage

Add versionator into your middleware stack before the static middleware:

      app.version = '0.1.0';
      var versionator = require('versionator');


      app.configure(function() {

      	app.use(versionator.createBasic(app.version).middleware('v' + app.version))
        ....
        .use(express.static(__dirname + '/public', { maxAge: 2592000000 }));

       });

Public folder:

	public/js/app.js

In your HTML,CSS,JS add the version as an extra path.

e.g.
### HTML
      <script src='/js/v0.1.0/app.js' />

You can of course manage this with a variable if you are using templating

### Templating
      <script src='/js/v#{app.version}/app.js' />

### Jade
      script(src='/js/v#{app.version}/app.js')


versionator will strip URL path names containing the version string. req.url is then modified for all other middleware.

e.g.

     req.url = '/js/v0.1.0/app.js'

will become:

     req.url = '/js/app.js'

Now all you need to do is increment app.version each deployment (We keep ours inline with our git tags using cake, the coffee-script build tool) then sit back and let your users enjoy the freshness.

An example of how to use versionator with connect and express can be found in the examples folder.

## Credits
[Paul Serby](https://github.com/serby/) Follow me on [twitter](http://twitter.com/PabloSerbo)

## Licence
Licenced under the [New BSD License](http://opensource.org/licenses/bsd-license.php)
