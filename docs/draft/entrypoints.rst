Entrypoints
-----------

The API will be reachable at <https://api.compat.mozilla.org>. A non-SSL
version will be reachable at <http://api.compat.mozilla.org>, and will
redirect to the SSL version.  This site is for applications that read,
create, update, and delete compatibility resources.  It includes a
browsable API to ease application development, but not full documentation.

The API supports two representations:

* `application/vnd.api+json` *(default)* - JSON mostly conforming to the
  [JSON API](http://jsonapi.org).
* `text/html` - the Django REST Framework browsable API.

The API supports user accounts with
[Persona](http://www.mozilla.org/en-US/persona/) authentication.  Persona
credentials can be exchanged for an [OAuth 2.0](http://oauth.net/2/) token
for server-side code changes.

A developer-centered website will be available at <https://compat.mozilla.org>.
A non-SSL version will be available at <http://compat.mozilla.org> and will
redirect to the HTTPS version.  This site is for documentation, example code,
and example presentations.

The documentation site is not editable from the browser.  It uses
gettext-style translations.  en-US will be the first supported language.

The two sites are served from a single codebase, at
<https://github.com/mozilla/compat-api>.  Technologies include:

* [Django 1.6](https://docs.djangoproject.com/en/1.6/), a web framework
* [Django REST Framework](http://www.django-rest-framework.org), an API
  framework
* [django-simple-history](https://django-simple-history.readthedocs.org/en/latest/index.html),
  for recording changes to models
* [django-hvad](http://django-hvad.readthedocs.org/en/latest/public/quickstart.html).
  for translations of human-facing text
* [django-mptt](https://github.com/django-mptt/django-mptt/), for efficiently
  storing hierarchical data
* [django-oauth2-provider](https://github.com/caffeinehit/django-oauth2-provider),
  for script-based updates of content

