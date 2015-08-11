title: Changelog Starter
date: 2015-08-11 12:00:00
categories: [Coding]
tags: [changelog, programming-style, coding-style, starter]
---

A [changelog] starter to be consistent with my coding style:

 - Two header levels only with setext style.
 - Use underlines for first-level headers and dashes for second-level headers.
 - Each release is a section, hence use second-level header.
 - Each category in a release is not a section, hence use emphasis with double asterisks.

[changelog]: https://en.wikipedia.org/wiki/Changelog


```md
Changelog
=========

Guideline:

1. Record notable changes.
2. List releases in reversed chronological order, newest on top.
3. Write all dates in `YYYY-MM-DD` format.
4. Follow [SemVer] format for each release.
5. Creating a new release by copying the **Example** section (on the bottom)
   to the top and rename it **Unreleased**.
6. Keep an **Unreleased** section on top for tracking any changes in order to
   minimize required effort.
7. Change _Unreleased_ to a release name with the date when ready.

_The guideline attempts to follow [Keep a CHANGELOG][1]._

[SemVer]: http://semver.org/
[1]: http://keepachangelog.com/


0.1.0 / 2015-01-01
------------------

**Security**

 - Inform users to upgrade for vulnerabilities.

**Removed**

 - Remove a deprecated feature in this release.

**Deprecated**

 - Deprecate a feature in the upcoming release.

**Changed**

 - Change an existing functionality.

**Added**

 - Add a new feature.

**Fixed**

 - Fix a bug.


Example / YYYY-MM-DD
--------------------

**Security**

 - Inform users to upgrade for vulnerabilities.
 - Each sentence must use the present tense and ends with a period.

**Removed**

 - Remove a deprecated feature in this release.
 - Basic Authentication is dropped in favor of Digest Authentication.

**Deprecated**

 - Deprecate a feature in the upcoming release.
 - Basic Authentication will be replaced by Digest Authentication in the next
   release.

**Changed**

 - Change an existing functionality.
 - User session data is added to the user object upon initialization.

**Added**

 - Add a new feature.
 - A new endpoint `/users` is added to handle user CRUD.

**Fixed**

 - Fix a bug.
 - Add the default case to handle unknown cases in user group assigning.
```
