# Heroku Buildpack for Ruby Oracle

Heroku buildpack for setting up Oracle Instant Client and the `LD_LIBRARY_PATH` so that Bundler can build [ruby-oci8](https://github.com/kubo/ruby-oci8)

# Usage

## Add a detect hook (required)

In order for this buildpack to execute, it will look for a `.oracle.yml` file in your app's root.  The file can be empty but it must exist.

    touch .oracle.yml

## Add Buildpack

You'll need to use multiple buildpacks. This buildpack will need to be invoked first, followed by [heroku-buildpack-ruby](https://github.com/heroku/heroku-buildpack-ruby).  Heroku now supports configuring multiple buildpacks natively, or you can use the [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi) buildpack.

### Setup using heroku-buildpack-multi

The benefit to using [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi) is that you can version-control your environment changes.

    heroku buildpacks:clear
    heroku buildpacks:set https://github.com/ddollar/heroku-buildpack-multi

Then inside `.buildpacks`, add the following contents:

    https://github.com/wealthsimple/oracle-heroku-buildpack
    https://github.com/heroku/heroku-buildpack-ruby

### Setup using heroku's native support for multiple buildpacks

Heroku natively also support's multiple buildpacks, the same setup as above can be achieved as follows:

    heroku buildpacks:clear
    heroku buildpacks:set https://github.com/heroku/heroku-buildpack-ruby
    heroku buildpacks:add --index 1 https://github.com/ddollar/heroku-buildpack-multi


# Configuration (Optional)

It is sometimes desirable to use `tnsnames.ora` or `sqlnet.ora` to configure how Oracle connects to a database or to use `sqlnet.ora` to configure connection wallets.

The `tnsnames.ora` and `sqlnet.ora` files are often located in `$ORACLE_HOME/network/admin`.  This buildpack will correctly setup `$ORACLE_HOME` and `$TNS_ADMIN` to point to `$ORACLE_HOME/network/admin`.  A location for `tnsnames.ora` or `sqlnet.ora` can be configured inside the `.oracle.yml` file:

    cat .oracle.yml
    ---
    tnsnames.ora: config/tnsnames.ora
    sqlnet.ora: config/sqlnet.ora

The files will be symlinked into `vendor/oracle-instantclient/network/admin`

You do not need both `tnsnames.ora` and `sqlnet.ora`, they are both optional, the buildpack and [ruby-oci8](https://github.com/kubo/ruby-oci8) will work fine without them.
