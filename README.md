# heroku-minion

Deployment and configuration tools for Heroku-based applications.

## What?

An command-line tool to automate and facilitate an opinionated
deployment workflow for Django applications on Heroku. Three critical
functions are included (with various options):

* Deployment to multiple environments
* Configuration of multiple environments
* Data migration between environments

## Why?

Heroku provides a really clear path for application development and
deployment through its [12-Factor app](http://12factor.net/) methodology,
and its use of `git push` deployments. This works really well at a simple
level (deploying the code), but when deploying a new version of a Django
application you may well have to run database migrations, push static
content to a CDN, and so on, and this can require some command-fu to
wrap it up into an idiot-proof, intelligent, deployment system. These steps
may be required for a single environment deployment, but when running a larger
multi-environment production workflow (e.g. dev -> uat -> live), the orchestration
of these steps can become complicated.

In addition, Heroku itself provides a number of useful deployment related
features - e.g. [preboot](https://devcenter.heroku.com/articles/labs-preboot/)
and [pipelines](https://devcenter.heroku.com/articles/labs-pipelines),
which can be used to reduce downtime and make deployments more robust.

This application is the result of working with Heroku over the course of 18 months
and the gradual, incremental, automation of deployments over that time. The story
is expanded upon on the YunoJuno [tech blog](http://tech.yunojuno.com/archive).

The workflow that this application supports is opinionated. If you want a super-
flexible do-anything tool, this isn't for you. It's primarily a tool for us,
YunoJuno, that we are open-sourcing.

## Deployment Workflow

The workflow that this tool supports is as follows:

* Three major environments - "dev", "uat", "live".
* Gitflow, or similar, as the underlying code development workflow
* Code migration *up* (dev > uat > live)
* Data migration *down* (live > uat > dev)
* **master** branch is only branch deployed to live environment

In plain english, the workflow relies on at least two branches - **dev** and **master**.
Using the Gitflow model, most work is done in dev (or on a branch off of dev
that is subsequently merged into dev - either way, it ends up in dev), and is
merged into master pre-release to live. The *dev* environment is considered
unstable, and anyone can deploy anything to that environment at any time. It is
the developers' responsibility. Deployments to *dev* are standard Heroku git
push deployments.

The *uat* and *live* environments are part of the live production site. The *live*
environment is self-explanatory - it's your live site. The *uat* environment is
the pre-production test site - it's where the next iteration of the code meets
the current state of the data. Deploying to *uat* is a de facto live deployment
so any problems in *live* should be uncovered here. The default behaviour of
Minion is to deploy the current **master** branch to the *uat* environment. This
can be overridden if required (although this is not recommended).

The *live* environment is your live website. There are much tighter controls
around this. It currently (subject to change) uses Heroku's
[pipelines](https://devcenter.heroku.com/articles/labs-pipelines) feature to
deploy the running 'slug' from *uat* to *live*. The pipelines feature can be
disabled, in which case, unlike the other environments, it is not possible to deploy
anything other than **master** to the *live* environment.

## Individual Deployments

For each deployment outlined above, Minion does a number of things:

* Intelligently infer what actions to take post-deployment (run migrations etc.)
* Intelligently infer whether the deployment requires downtime
* Wrap any downtime with the Heroku maintenance page
* Tag the repo with the Heroku release number post-deployment

Deployments all have a confirmation code, to prevent any accidental deployments.
Prior to the confirmation the app outputs a report on all the action that are
about to be taken, and a review of the commits that are about to be deployed.
This is done by comparing the local repo with the Heroku release commit hash on
the remote environment. A sample pre-deployment output is shown below:

```
$ fab deploy:live

File changes:

* admin.py

Git log since latest release (v423):

* 286ea45  Fix for 1397.

Options have been automatically set to a value of 1 from the contents of the deployment.

----------------------------
Target environment:  live
Git branch:          master
Git remote:          git@heroku.com:my-app-123.git
Force push:          False
Preboot:             True
Pipeline:            True
Heroku app:          my-app-123

Options:             1

[1] Git push:        True
[2] Syncdb:          False
[4] Migrations:      False
[8] Load fixtures:   False
[16] Collect static: False
----------------------------
Please confirm your intention to continue by typing in '957766' at the prompt: 957766
```

More details on the background to intelligent Heroku deployments can be found on
the YunoJuno tech blog:

* [Deploying Django apps to Heroku (#3)](http://tech.yunojuno.com/deploying-django-apps-to-heroku-3)
* [Deployment Changelog](http://tech.yunojuno.com/deployment-changelog)
* [Turbo deployments with Heroku 'Preboot'](http://tech.yunojuno.com/turbo-deployments-with-heroku-preboot.html)

## Configuration

The configuration feature facilitates the management of Heroku environment
variables.

## Data Migration

The data migration feature uses Heroku's `pgbackups` feature to migrate data
between environments.

VERY IMPORTANT NOTICE.

**If you have secure user data on your live environment you must behave in
accordance with any / all data protection regulations. Our data migration
feature depends upon the existence of an appropriate Django user anonymisation 
management task. Any data that is migrated off of the *live* environment will
be anonymised. It is *your* responsibility to ensure that this management
task is doing the correct thing.**

NB This relies on the use of Heroku's postgres database service.


** If you don't know what Heroku is, this repo isn't for you.
