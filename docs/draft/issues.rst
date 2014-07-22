Issues to Resolve Before Code
=============================

Additions to Browser Compatibility Data Architecture
----------------------------------------------------

This spec includes changes to the
[Browser Compatibility Data Architecture](https://docs.google.com/document/d/1YF7GJ6kgV5_hx6SJjyrgunqznQU1mKxp5FaLAEzMDl4/edit#)
developed around March 2014.  These seemed like a good idea to me, based on
list threads and thinking how to recreate Browser Compatibility tables live on
MDN.

These changes are:

* **browsers**
    - **slug** - human-friendly unique identifier
    - **name** - converted to localized text.
    - **environment** - either "desktop" or "mobile".  Supports current division
      of browser types on MDN.
    - **engine** - either the localized engine name or null.  Supports current
      engine version callouts on MDN tables.
* **browser-versions**
    - **engine-version** - Engine version, used in Firefox version numbers
    - **release-day** - Day of release
    - **retirement-day** - Day of "retirement", or when it was superceeded by
      a new release.
    - **status** - One of "retired", "retired-beta", "current", "beta",
      "future"
    - **relese-notes-uri** - For Mozilla releases, as specified in
      [CompatGeckoDesktop](https://developer.mozilla.org/en-US/docs/Template:CompatGeckoDesktop).
* **features**
    - **slug** - human-friendly unique identifier
    - **maturity** - the standards maturity of the feature.  One of "standard"
      (no special markup), "non-standard" (non-standard flag, like
      the `left` and `right` features of
      [Web/CSS/caption-side](https://developer.mozilla.org/en-US/docs/Web/CSS/caption-side#Browser_compatibility)),
      or "experimental" (beaker flag, like the `run-in` value of
      [Web/CSS/display](https://developer.mozilla.org/en-US/docs/Web/CSS/display#Browser_compatibility)).
    - **canonical** - True if the name is the code used by a developer
    - **name** - converted to localized text, or lang 'zxx' if canonical
    - **specfication-sections** - replaces spec link
* **feature-sets**
    - **slug** - human-friendly unique identifier
    - **kuma-path** - MDN path that data was scraped from
    - **canonical** - True if the name is the code used by a developer
    - **name** - converted to localized text, or lang 'zxx' if canonical
    - **ancestors**, **siblings**, **children**, **decendants** - tree relations
* **browser-version-features**
    - **support** - Added a value "never", for cases where developers have
      announced they will not support a feature.  For example, the
      [CSS @font-face at-rule](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face#Specifications),
      which has been closed as WONTFIX in
      [Bugzilla 119490](https://bugzilla.mozilla.org/show_bug.cgi?id=119490),
      appears as `{{CompatNo}}{{unimplemented_inline(119490)}}`.
    - **prefix** - string prefix to enable, or null if no prefix
    - **note** - short note, length limited, translated, or null.  Supports
      inline notes currently in use on MDN
    - **footnote** - longer note, may include code samples, translated, or null.
      Supports extended footnote in use on MDN.

There are also additional Resources:

* **specification-sections** - For referring to a section of a specification, with
  translated titles and anchors
* **specifications** - For referring to a specification, with translated titles
  and URIs.
* **specification-statuses** - For identifying the process stage of a spec
* All the history resources (**browsers-history**,
  **browser-versions-history**, etc.)
* **users** - For identifying the user who made a change
* **changesets** - Collect several history resources into a logical change

Unresolved Issues
-----------------

* We've been talking data models.  This document talks about APIs.
  **The service will not have a working SQL interface**.  Features like
  history require that changes are made through the API.  Make sure your
  use case is supported by the API.
* overholt wants
  [availability in Web Workers](https://bugzilla.mozilla.org/show_bug.cgi?id=996570#c14).
  Is an API enough to support that need?
* I'm not sure if the translatable strings are correct:
    - browsers.name - Firefox explicitly says
      [don't localize our brand](http://www.mozilla.org/en-US/styleguide/communications/translation/#branding).
      I can't find examples of any localized browser names in the wild.
    - browsers.engine - Same
    - features.name - "Basic usage" and "none, inline and block" should be
      localized.  But, are those good feature names?  Could you write a bit of
      JavaScript to test 'Basic usage'?  Should there be three features
      ("display: none", "display: inline", "display: block") instead?  The
      need for translated feature names might be a doc smell.  The canonical
      flag might turn into a list of tables to fix.
    - feature-sets.name - Not sure if "CSS", "display", etc. should be
      localized, similar logic to feature names.  The canonical flag will
      help.
* I think Niels Leenheer has good points about browsers being different across
  operating systems and OS versions - I'm considering adding this to the model:
  [Mapping browsers to 2.2.1 Dictionary Browser Members](http://lists.w3.org/Archives/Public/public-webplatform-tests/2013OctDec/0007.html).
* How should we support versioning the API?  There is no Internet concensus.
    - I expect to break the API as needed while implementing.  At some point
      (late 2014), we'll call it v1.
    - Additions, such as new attributes and links, will not cause an API bump
    - Some people put the version in the URL (/v1/browsers, /v2/browsers)
    - Some people use a custom header (`X-Api-Version: 2`)
    - Some people use the Accept header
      (`Accept: application/vnd.api+json;version=2`)
    - These people all hate each other.
      [Read a good blog post on the subject](http://www.troyhunt.com/2014/02/your-api-versioning-is-wrong-which-is.html).
* What should be the default permissions for new users?  What is the process
  for upgrading or downgrading permissions?
* Is Persona a good fit for creating API accounts?  There will need to be a
  process where an MDN user becomes an API user, and a way for an API user
  to authenticate directly with the API.
* If we succeed, we'll have a detailed history of browser support for each
  feature.  For example, the datastore will know that every version of Firefox
  supported the `<h1>` tag.  How should version history be summarized for the
  Browser Compatibility table?  Should the API pick the "important" versions,
  and the KumaScript display them all?  Or should the API send all known
  versions, and the KumaScript parse them for the significant versions, with
  UX for exposing known versions?  The view doc proposes one implementation,
  with a `<meta>` section for identifying the important bits.
* Do we want to add more items to browser-versions?  Here's the Wikipedia
  release history for
  [Chrome](http://en.wikipedia.org/wiki/Google_Chrome_complete_version_history#Release_history)
  and
  [Firefox](http://en.wikipedia.org/wiki/Firefox_release_history#Release_history).
  Some possibly useful additions: release date, retirement date, codename,
  JS engine version, operating system, notes.  It feels like we should import
  the data from version-specific KumaScripts like
  [CompatGeckoDesktop](https://developer.mozilla.org/en-US/docs/Template:CompatGeckoDesktop)
  (versions, release dates, translations, links to release docs).
* We'll need additional models for automated browser testing.  Things like
  user agents, test names, test results for a user / user agent.  And, we'll
  need a bunch of rules for mapping test results to features, required number
  of tests before we'll say a browser supports a feature, what to do with
  test conflicts, etc.  It might be easier to move all those wishlist items to
  a different project, that talks to this API when it's ready to assert
  browser support for a feature.
* groovecoder says that api.compat.mozilla.org is a non-starter with Mozilla
  IT - too hard to provision a new domain.  He suggests a subpath under
  developer.mozilla.org.  Should we go with:

      1. https://api.compat.mozila.org and https://compat.mozilla.org
      2. https://developer.mozilla.org/compat-api and
         https://developer.mozilla.org/en-US/compat-api
      3. https://api.compat.developer.mozilla.org and
         https://compat.developer.mozilla.org
      4. Something else?


Interesting MDN Pages
---------------------

These MDN pages represent use cases for compatability data.  They may suggest
features to add, or existing features that will be dropped.

* [Web/HTML/Element/address](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/address#Specifications) -
  A typical "simple" example.  However, the name is non-canonical
  ("Basic Features") and must be translated, rather than a canonical form
  ("`<address>`") that could be the same for all languages.
* [Web/CSS/display](https://developer.mozilla.org/en-US/docs/Web/CSS/display#Specifications) -
  This complex page includes non-canonical names
  ("`none,inline` and `block`"), experimental features (`run-in`), support
  changes across versions, prefixes, etc.  Everything that makes this project
  hard.
* [Web/CSS/cursor](https://developer.mozilla.org/en-US/docs/Web/CSS/cursor#Specifications) -
  May be more complex than `display`.
* [Web/HTML/Element/Input](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#Browser_compatibility) -
  Complex, with lots of attributes.  Split by standard may not be as useful
  as other ways to split it.
* [Web/CSS/animation-name](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-name#Specifications) -
  New property that moved from prefixed support to standard support.
* [Web/CSS/caption-side](https://developer.mozilla.org/en-US/docs/Web/CSS/caption-side#Specifications) -
  Rarely used 'Non-standard' tag.  Also seen on
  [Web/CSS/text-align](https://developer.mozilla.org/en-US/docs/Web/CSS/text-align#Specifications)
* [Web/CSS/@font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face#Specifications) -
  Rarely used 'Unimplemented' tag as inline note.  Also seen on
  [Web/CSS/text-decoration-line](https://developer.mozilla.org/en-US/docs/Web/CSS/text-decoration-line#Specifications).
* [Web/CSS/length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#Browser_compatibility) -
  Rarely used "warning" tag.  Also seen on
  [Web/CSS/text-underline-position](https://developer.mozilla.org/en-US/docs/Web/CSS/text-underline-position#Specifications).
* [Web/CSS/line-break](https://developer.mozilla.org/en-US/docs/Web/CSS/line-break#Specifications) -
  Rarely used "Fix Me" inline note
* [Web/CSS/min-height](https://developer.mozilla.org/en-US/docs/Web/CSS/min-height#Specifications) -
  "Obsolete since Gecko 22" tag on auto, versus:
* [Web/CSS/min-width](https://developer.mozilla.org/en-US/docs/Web/CSS/min-width#Specifications) -
  Obsolete trash can icon
* [Web/CSS/text-transform](https://developer.mozilla.org/en-US/docs/Web/CSS/text-transform#Specifications) -
  Interesting use of non-ascii unicode in feature names, good test case.
* [Web/CSS/transform-origin](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-origin#Specifications) -
  IE may justify a 'alternate' value for browser-version-features.support, or
  just 'no' with a footnote.

Some pages will require manual intervention to get them into the data store.
Here's a sample:

* [Web/CSS/box-decoration-break](https://developer.mozilla.org/en-US/docs/Web/CSS/box-decoration-break#Specifications) -
  Broken formatting
* [Web/CSS/box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing#Specifications) -
  In Safari column, link to engine version will become an inline note.
* [Web/CSS/break-inside](https://developer.mozilla.org/en-US/docs/Web/CSS/break-inside#Specifications) -
  Will need to add a skeleton compatibility table.
* [Web/CSS/@document](https://developer.mozilla.org/en-US/docs/Web/CSS/@document#Specifications) -
  Specification paragraph rather than normal table.
* [Web/CSS/clip](https://developer.mozilla.org/en-US/docs/Web/CSS/clip#Specifications) -
  Long inline notes should be converted to footnotes.
* [Web/CSS/:invalid](https://developer.mozilla.org/en-US/docs/Web/CSS/:invalid#Specifications) -
  Links in feature names to other MDN docs
* [Web/CSS/outline-color](https://developer.mozilla.org/en-US/docs/Web/CSS/outline-color#Specifications) -
  Instead of version, long note about support.  Convert to two versions, footnote.
* [Web/CSS/radial-gradient](https://developer.mozilla.org/en-US/docs/Web/CSS/radial-gradient#Specifications) -
  Evolving standard, used version notes instead of marking feature as
  experimental or deprecated.
* [Web/CSS/ratio](https://developer.mozilla.org/en-US/docs/Web/CSS/ratio#Specifications) -
  Strange Chrome version
* [Web/CSS/tab-size](https://developer.mozilla.org/en-US/docs/Web/CSS/tab-size#Specifications) -
  Lots of interesting versions, including Safari nightly.
* [Web/CSS/text-rendering](https://developer.mozilla.org/en-US/docs/Web/CSS/text-rendering#Specifications) -
  convert to footnotes, other changes needed.  Not sure if it belongs under CSS.
* [Web/API/IDBObjectStore](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore#Specifications) -
  apoplectic warning of Chrome behaviour.  Maybe convert to regular note, or
  add a Feature for Chrome prefix with non-standard tag?

Translating from MDN wiki format
--------------------------------

The current compatibility data on developer.mozilla.org in MDN wiki format, a
combination of HTML and KumaScript.

A MDN page will be imported as a feature-set.

.. highlight::html

Here's the MDN wiki version of the Specifications section for
[Web/CSS/border-image-width](http://developer.mozilla.org/en-US/docs/Web/CSS/border-image-width)::


    <h2 id="Specifications" name="Specifications">Specifications</h2>
    <table class="standard-table">
      <thead>
        <tr>
          <th scope="col">Specification</th>
          <th scope="col">Status</th>
          <th scope="col">Comment</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>{{SpecName('CSS3 Backgrounds', '#border-image-width', 'border-image-width')}}</td>
          <td>{{Spec2('CSS3 Backgrounds')}}</td>
          <td>Initial specification</td>
        </tr>
      </tbody>
    </table>

The elements of this table are converted into API data:

* **Body row, first column** - Format is `SpecName('KEY', 'PATH', 'NAME')`.
  `KEY` is the specification.kuma-key, `PATH` is specification-section.subpath,
  in the page language, and `NAME` is specification-section.name, in the page
  language.  The macro
  [SpecName](https://developer.mozilla.org/en-US/docs/Template:SpecName) has
  additional lookups on `KEY` for specification.name and specification.uri (en
  language only).
* **Body row, second column** - Format is `Spec2('KEY')`.  `KEY` is the
  specification.kuma-key, and should match the one from column one.  The macro
  [Spec2](https://developer.mozilla.org/en-US/docs/Template:Spec2) has
  additional lookups on `KEY` for specification-status.kuma-key, and
  specification-status.name (multiple languages).
* **Body row, third column** - Format is a text fragment which may include HTML
  markup, becomes the specification-section.name associated with this
  feature-set.

and here's the Browser compatibility section::

    <h2 id="Browser_compatibility">Browser compatibility</h2>
    <div>{{CompatibilityTable}}</div>
      <div id="compat-desktop">
        <table class="compat-table">
          <tbody>
            <tr>
              <th>Feature</th>
              <th>Chrome</th>
              <th>Firefox (Gecko)</th>
              <th>Internet Explorer</th>
              <th>Opera</th>
              <th>Safari</th>
            </tr>
            <tr>
              <td>Basic support</td>
              <td>15.0</td>
              <td>{{CompatGeckoDesktop("13.0")}}</td>
              <td>11</td>
              <td>15</td>
              <td>6</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div id="compat-mobile">
        <table class="compat-table">
          <tbody>
            <tr>
              <th>Feature</th>
              <th>Android</th>
              <th>Firefox Mobile (Gecko)</th>
              <th>IE Phone</th>
              <th>Opera Mobile</th>
              <th>Safari Mobile</th>
            </tr>
            <tr>
              <td>Basic support</td>
              <td>{{CompatUnknown}}</td>
              <td>{{CompatGeckoMobile("13.0")}}</td>
              <td>{{CompatNo}}</td>
              <td>{{CompatUnknown}}</td>
              <td>{{CompatUnknown}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

This will be converted to API resources:

* **Table class** - one of "compat-desktop" or "compat-mobile", sets
  browser.environment.
* **Header row, all but the first column** - format is either
  `Browser Name (Engine Name)` or `Browser Name`.  Used for browser.name,
  browser.engine-name (set or `null`).  Other formats or KumaScript halt
  import.
* **Non-header rows, first column** - If the format
  is `<code>some text</code>`, then feature.canonical=true and the
  string is the canonical name.  If the format is text w/o KumaScript, it
  is the non-canonocial name.  If there is also KumaScript, it varies:

    * TODO: doc KumaScript
* **Non-header rows, remaining columns** - Usually Kumascript:
    * `{{CompatUnknown}}` - browser-version.version and
      browser-version.engine-version are `null`, and
      browser-version-feature.support is `"unknown"`
    * `{{CompatVersionUnknown}}` - browser-version.version and
      browser-version.engine-version are `null`, and
      browser-version-feature.support in `"yes"`
    * `{{CompatNo}}` - browser-version.version and
      browser-version.engine-version are `null`, and
      browser-version-feature.support is `"no"`
    * `{{CompatGeckoDesktop("VAL")}}` - browser-version.version is
      set to `"VAL"`, browser-version-feature.support is `"yes"`.
      browser-version.engine-version and browser-version.release-day is set by
      logic in
      [CompatGeckoDesktop](https://developer.mozilla.org/en-US/docs/Template:CompatGeckoDesktop).
    * `{{CompatGeckoMobile("VAL")}}` - browser-version.version is
      set to `"VAL"`, browser-version-feature.support is `"yes"`.
      browser-version.engine-version is set by logic in
      [CompatGeckoMobile](https://developer.mozilla.org/en-US/docs/Template:CompatGeckoMobile).
    * Numeric string, such as `6`, `15.0`.  This becomes the
      browser-version.version, browser-version.engine-version is `null`, and
      browser-version-feature.support is `"yes"`.
* **Content after table** - This is usually formatted as a paragraph,
  containing HTML.  It should become browser-version-features.footnotes,
  but it will challenging to auto-parse and associate.

Data sources for browser versions
---------------------------------

The **browser-version** model currently supports a release date and a
retirement date, as well as other version data.  Some sources for this data
include:

* Google Chrome
    - [Google Chrome Release History](http://en.wikipedia.org/wiki/Google_Chrome#Release_history)
      on Wikipedia
* Mozilla Firefox
    - [Firefox Release History](http://en.wikipedia.org/wiki/Firefox_release_history)
      on Wikipedia
    - [Template:CompatGeckoDesktop](https://developer.mozilla.org/en-US/docs/Template:CompatGeckoDesktop)
* Microsoft Internet Explorer
    - [Release History for desktop Windows OX version](http://en.wikipedia.org/wiki/Internet_Explorer_1#Release_history_for_desktop_Windows_OS_version)
      on Wikipedia
* Opera
    - [Opera version history](http://www.opera.com/docs/history/) on opera.com
    - [Opera presto history](http://www.opera.com/docs/history/presto/)
      on opera.com
* Safari
    - [Safari version history](http://en.wikipedia.org/wiki/Safari_version_history#Release_history)
      on Wikipedia


To Do
-----

* Add multi-get to browser doc, after deciding on
  `GET /browser-versions/1,2,3,4` vs.
  `GET /browser/1/browser-versions`
* Look at additional MDN content for items in common use
* Move to developers.mozilla.org subpath, auth changes
* Jeremie's suggested changes:
    * Add browsers.notes, localized, to note things like engine, applicable
      OS, execution contexts (web workers, XUL, etc.).
    * Drop browsers.environment attribute.  "deskop", "mobile" doesn't cover
      the world of browsers.  This is a UX change to MDN Browser compatability
      tables.
    * Drop browsers.engine attribute.  Not important for searching or
      filtering, instead free text in browsers.notes
    * Add browser-versions.notes, localized, to note things like OS, devices,
      engines, etc.
    * Drop browser-versions.engine-version, not important for searching or
      sorting.
    * Drop browser-versions.status.  Doesn't think the MDN team will be able
      to keep up with browser releases.  Will instead rely on users
      figuring out if a browser version is the current release.
    * Drop feature.canonical.  Instead, name="string" means it is
      canonical, and name={"lang": "translation"} means it is non-canonical.
    * Feature-sets is a cloud, not a heirarchy.  "color=red" is the same
      feature as "background-color=red", so needs to be multiply assigned.
    * A feature-set can either have sub-feature sets (middle of cloud), or
      features (edge of cloud).
    * Add browser-version-feature-sets, to make positive assertions about
      a browser-version supporting a feature-set.  Only negative assertions
      can be made based on features.
    * Drop order of features by feature set.  Client will alpha-sort.
    * browser-version-features.support, drop "prefixed" status.  If prefixed,
      support = 'yes', and prefix is set.
    * Add examples of filtering (browser versions in 2010, firefox versions
      before version X).
* Holly's suggestions
    * Nail down the data, so she has something solid to build a UX on.
    * sheppy or jms will have experience with how users use tables and
      contribute to them, how frequently.
