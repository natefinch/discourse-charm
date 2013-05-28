# About

This charm installs the Discourse application, http://discourse.org

# Install

After you've successfully bootstrapped an environment, run the following:

    juju deploy cs:~marcoceppi/discourse
    juju deploy postgresql

Then create relations

    juju add-relation discourse postgresql:db-admin

Discourse requires `db-admin` level access in order to enable the hstore
plugin. Finally, expose discourse:

    juju expose discourse

# Configure

There are several configuration options you can use to manage your Discourse
deployment. With the exception of admins, all defaults should suffice for most
deployments. Take care when adjusting other options as they may produce an
unstable deployment if unexpected values are used.

## admins

In order to access the Discourse Admin tools you must specify which users
should be admins. After creating your account in Discourse, add your name and
the name of others who should be admin using this configuration option. The
option takes a string of comma separated usernames. You can find a username by
visiting a user's profile and copying the last part of the URL, in this case
"marcoceppi": `http://meta.discourse.org/users/marcoceppi`. To set just one
admin do the following:

    juju set discourse admins="marcoceppi"

Where "marcoceppi" is the username. For multiple admins use commas to
separate values:

    juju set discourse admins="marcoceppi,codinghorror,sam,eviltrout"

In the event you wish to remove an admin, say `sam`, simply supply the
same list of users again while removing the user you wish to not have access:

    juju set discourse admins="marcoceppi,codinghorror,eviltrout"

If a user does not exist they will not be granted admin access and will
simply be skipped.

## repository

If you maintain a forked version of the Discourse repository, you can deploy or
swap to that modified repository at any time. It's recommended you use upstreams
git repo at `https://github.com/discourse/discourse.git` or at least monitor it
for security and feature updates.

    juju set discourse repository="https://code.example.tld/repo/discourse.git"

In order to make use of the `version` option, you will need to push to your
Discourse repo with the `--tags` option. Otherwise git tags will not be moved
over which the `version` option relies on.

## release

Release is an easy way to "pin" your install at a known and familiar version of
Discourse, allowing you to "upgrade" to another version when you're ready. By
default this Discourse charm deploys against the "latest-release" Git tag which
is pointed at the latest version of Discourse.

For production deployments of Discourse it's recommended you change this version
either during deployment or shortly after to the corrosponding [release](https://github.com/discourse/discourse/tags)
of Discourse you deployed. Doing so will ensure that during scale-out you'll
maintain the same version of Discourse between units. Failure to do so may
result in broken behavior due to version inconsistency between units.

    juju set discourse version="v0.9.1"

Release can be any valid `git tag` in the given `repository`

## branch

If you're using a custom repository and wish to point to a branch other than
the master, then use this option to change from the default "master" to another
valid branch name in your repository. At the time of this writing all upstream
versions are maintained by git tags. To switch to a different release of Discourse
use the `release` configuration option for this charm.

    juju set discourse branch="not-master"

# Extras

These are additional things you can do to enhance your base install.

## Shared queuing server

If you have a need for a scaled out queuing server, or if you find yourself
with many discourse units, it will be beneficial to use a separate redis
server for queue management. To conserve resources all units, by default,
have their own `redis-server` installed. However, you can deploy and add
a relation to the [redis-master](http://jujucharms.com/charms/precise/redis-master)
charm in order to provide a central redis server.

    juju deploy redis-master redis
    juju add-relation discourse redis:db

In doing so the local `redis-server` installs will be removed and the site
re-configured to use the new redis service. For more information on scaling
out redis, refer to the [redis-slave](http://jujucharms.com/charms/precise/redis-slave)
charm.

# Additional Help

Nothing yet?
