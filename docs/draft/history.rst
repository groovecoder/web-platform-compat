History Resources
=================

History Resources are created when a Resource is created, updated, or deleted.
By navigating the history chain, a caller can see the changes of a resource
over time.

All history representations are similar, so one example should be enough to
determine the pattern.

Browsers History
----------------

A **browsers-history** represents the state of a **browser** at a point in
time, and who is responsible for that state.  The representation includes:

* **attributes**
    - **id** *(server selected)* - Database ID
    - **timestamp** *(server selected)* - Timestamp of this change
    - **event** *(server selected)* - The type of event, one of "created",
      "changed", or "deleted"
    - **browsers** - The **browsers** representation at this point in time
* **links**
    - **browser** *(one)* - Associated **browser**, can not be changed
    - **changeset** *(one)* - Associated **changeset**, can not be changed.

To get a single **browsers-history** representation::

    GET /browsers-history/1002 HTTP/1.1
    Host: api.compat.mozilla.org
    Accept: application/vnd.api+json

::

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "browsers-history": {
            "id": "1002",
            "timestamp": "1404919464.559140",
            "event": "created",
            "browsers": {
                "id": "2",
                "slug": "firefox",
                "environment": "desktop",
                "icon": "//compat.cdn.mozilla.net/media/img/browsers/firefox.png",
                "name": {
                    "en": "Firefox"
                },
                "engine": {
                    "en": "Gecko"
                },
                "links": {
                    "versions": ["124"],
                    "history-current": "1002",
                    "history": ["1002"]
                }
            },
            "links": {
                "browser": "1",
                "changeset": "1",
            }
        },
        "links": {
            "browsers-history.browser": {
                "href": "https://api.compat.mozilla.org/browser-history/{browsers-history.browser}",
                "type": "browsers"
            },
            "browsers-history.changeset": {
                "href": "https://api.compat.mozilla.org/changesets/{browsers-history.changeset}",
                "type": "changeset"
            }
        }
    }

Browser Versions History
------------------------

A **browser-versions-history** represents a state of a **browser-version** at
a point in time, and who is responsible for that representation.  See
**browsers-history** and **browser-versions** for an idea of the represention.

Features History
----------------

A **features-history** represents a state of a **feature** at a point in time,
and who is responsible for that representation.  See **browsers-history** and
**features** for an idea of the represention.

Feature Sets History
--------------------

A **feature-sets-history** represents a state of a **feature-set** at a point
in time, and who is responsible for that representation.  See
**browsers-history** and **feature-sets** for an idea of the represention.

Browser Version Features History
--------------------------------

A **browser-version-features-history** represents a state of a
**browser-version-feature** at a point in time, and who is responsible for that
representation.  See **browsers-history** and **browser-version-features** for
an idea of the represention.

