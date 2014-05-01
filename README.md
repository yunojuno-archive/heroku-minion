WIP


heroku-minion
=============

Deployment and configuration tools for Heroku-based applications.

What?
-----

Heroku provides a really clear path for application development and
deployment through its [12-Factor app](http://12factor.net/) methodolgy,
and its use of `git push` deployments. This works really well at a simple
level (deploying the code), but when deployming a new version of a Django
application you may well have to run database migrations, push static
content to a CDN, and so on, and this can require some command-fu to
wrap it up into an idiot-proof, intelligent, deployment system.

This is multiplied if you have a deployment workflow that involves
pushing code through several environments on its way to live.



```shell
$ fab heroku_deploy_app:uat

Git changelog since latest release (v81):

* 4ea1479 Improved warning message for search validation errors.

The following files have changed:

* [redacted]/views.py

Options have been automatically set to a value of 1 from the
contents of the deployment.

----------------------------
Target environment:  uat
Git branch:          master
Git remote:          git@heroku.com:[redacted].git
Force push:          False
Preboot:             True
Heroku app:          [redacted]

Options:             1

[1] Git push:        True
[2] Syncdb:          False
[4] Migrations:      False
[8] Load fixtures:   False
[16] Collect static: False
----------------------------

Please confirm your intention to continue by typing in '245631' at the prompt:
```

















** If you don't know what Heroku is, this repo isn't for you.
