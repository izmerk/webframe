Webframe
========

When you just want a goddamned website
--------------------------------------

If I were to say something about "the purpose of webframe is to provide an unobtrusive yet powerful blah blah blah" I'd be lying because I wrote this for myself because I wanted a library I knew how to use and could be held accountable for. That being said, I think it's pretty cool.

Organization
------------

Webframe is designed to run either behind apache as Python CGI or as its own server, though that part is a little shaky at the moment. It either strongly recommends or requires the following design tennants:

- Each site runs out of a folder and all nondata requests are served by index.py
- Each site has a `data/` folder which contains the static data needed for rendering the site, with the exception of the data/user folder which can hold file uploads and such.
- `data/lib` is symlinked to a basic library that you can reuse for all your sites.
- MySQL as the database
- You work seperately from your servers and update them through git

Setup (Apache/CGI)
------------------

Do the following on the server side:
- Make your standard `lib/` directory somewhere
- Make a `python/` directory inside `lib/` and `cd` to it
- Clone this repository: `git clone https://github.com/rhelmot/webframe.git`

And then for each website you set up:
- Configure apache to serve files out of a certain directory. Make sure that the `AllowOverride` directive is set to `all`
- `cd` to that folder
- Run the following: `[path to]lib/python/webframe/webframe_init.sh`
- Input any inforation it asks you for
- When it finishes, you can clone your repository with the line it gives you. If you want to be able to test your site locally, be sure to repeat the `lib/python/webframe` directory-creating step!

Here's a basic test to see if you can run webframe:
- Clone this repository
- Add `127.0.0.1 example.com` to /etc/hosts
- Run `server.py`
- Load http://example.com

Setup (Standalone)
------------------

*At the moment, this is pretty darned unstable. Use at your own risk.*

Do the following on the server side:
- Make a folder for everything
- Clone this repository: `git clone https://github.com/rhelmot/webframe.git`

And then for each website you set up:
- Make a folder for each domain you'd like to host
- `cd` to that folder
- Run the following: `../webframe_init.sh`
- Input any information it asks you for
- When it finishes, you can clonse your repository with the line it gives you. If you want to be able to test your site locally, you can just set up the server locally!

To start the server, run `sudo python server.py`

Templating
----------

Webframe has an easy-to-use templating system. Here is an overview of its features:
- `webframe.util.template( (<template string> | <path to template file>), <content dict>, cache=False)`
- `{{keyname}}` will be replaced with `value` if the dict contains `'keyname': 'value'`
- `{{~if auth=admin}}Controls{{~elif auth}}Logged in{{~else}}Anonymous{{~endif}}` yields different values depending on the value of `auth` in the content dict
    - All values are truthy except for `False`, `"False"`, `None`, `0`, and `''`
    - `{{~if}}` blocks do not nest
    - No logical operations or tests other than equality are supported
- If you're loading the same template file many times, set the third argument to `True` to cache its value

Databases
---------

- Provide database information to init script
- Initialize with `webframe.data.connect(<database name>)`
- `webframe.data.query(query_string, *args)` runs a query (False on failure) whose results can be accessed through `webframe.data.getRow()` and `webframe.data.getAll()`
- `webframe.data.getQuery(query_string, *args)` returns a tuple of all returned rows or False on failure
- Rows are returned as dicts, with common types parsed
- Prepared-esque queries supported-- The query string is parsed by webframe.util.template()
- Supports sanitation by escaping backslashes and double quotes in templated items. *Only use double-quotes in your queries.*
- Uses `mysqldb` for querying stuff
