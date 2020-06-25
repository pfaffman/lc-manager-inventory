# lc-manager-inventory

The Literate Computing Discourse Manager is a service that allows managing a Discourse server. It can run routine upgrades (including mail receiver and PG10->PG12 upgrades), add plugins, and apply Site Settings via the API.

For developers, it can also completely re-write your `web_only.yml` file to pin Discourse and any plugins to specific commits.

It can auto-detect whether you have a standard `app.yml` (as created with `./discourse-setup`) or standard 2-container `web_only.yml` + `data.yml` (as created with `./discourse-setup 2container`) installation. It can also manage multiple yml files for multi-server setups with a reverse proxy.

## Simple setup

A host stanza looks like this:

```
        test.myforum.us:
          discourse_name: "Jay Pfaffman" # your name
          discourse_api_key: "xxx" # system API key, used to apply settings
          do_api_key: '1234' # digital ocean API key; future use: create droplet and install
          # any plugins that you want to ADD to your current app.yml
          discourse_custom_plugins:
            - https://github.com/discoursehosting/discourse-suppress-category-from-latest.git
            - https://github.com/discourse/discourse-data-explorer.git
          ansible_user: ubuntu # if this is omitted, defaults to root
          # ENV settings, for example:
          discourse_extra_envs:
            - "DISCOURSE_S3_ACCESS_KEY_ID: 'AKIA
            - "DISCOURSE_S3_SECRET_ACCESS_KEY: 'pHMP'
            - "DISCOURSE_S3_REGION: 'us-east-1'"
            - "DISCOURSE_S3_BACKUP_BUCKET: 'my-great-bucket/backups'"
            - "DISCOURSE_BACKUP_LOCATION: 'S3'"
          # settings to be applied via the API after deployment
          discourse_custom_settings:
            maximum_backups: 30
            backup_frequency: 1
            backup_with_uploads: true

```

Currently getting your deployment set up requires a few manual steps that will will work with you to get in place. This includes:

1. Clone this repo for your own configuration
1. Create a user for you on our deployment server
1. Getting your ssh keys for your host(s) in place for your user
1. Seeing that your user can clone your repo
1. Creating a cron job to pull your repo so that you can make changes and request deployments

Once this is in place, you can

```
touch upgrade-hosts/HOSTNAME.upgrade
git add upgrade-hosts/HOSTNAME.upgrade
git commit -m "add the ad plugin"
git push
```

Our server will pull your repo, see the upgrade file, and automatically run the upgrade. When it's done, it'll push a file to your repo and include a commit message like "Deployment of HOSTNAME succeeded f8bd88 2020-06-25-1500"

It's that simple.
