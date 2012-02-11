rbenv installer
===============

This tool is used to install `rbenv` and some plugins. It also provides 
scripts to install required software to be able to compile **Ruby**.

Installed plugins are:

- rbenv-vars
- ruby-build
- rbenv-installer


Before Installing
-----------------

Install `git`:

    apt-get -y install git-core

Make sure your user has `sudo` privileges.


Install
-------

Install [rbenv] and friends by running:

    curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash

To avoid hand-rolled ruby installations and packaged ruby installation, but rather
manage rbenv user and system installs using Chef-Solo and fnichol's [ruby_build] and [rbenv]
cookbooks, bootstrap an initial ruby (1.9.2-p290) + chef + bundler install:

    sudo curl -L https://raw.github.com/hedgehog/rbenv-installer/sysinstall/bin/rbenv-bootstrap-chef-solo | bash

After this your production installations of ruby can be managed by chef.
Example: install a different Ruby system-wide and then remove this bootstrapped version :)

System-wide Install
-------------------

Install [rbenv] and friends (`system-update`)by running:

    curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-system-installer | bash

Consistent with a lean install, this will install, only the master branches of the plugins:
`rbenv-vars`, `ruby-build`, `rbenv-installer` and `rbenv-bundler`.

Configuration specifics are: `/etc/profile.d/rbenv.sh`, `/etc/gemrc`, `/etc/skel/.gemrc` and `/etc/skel/.bundle/config`,
which contain sane defaults for a production installation of [rbenv] + [Bundler].
In addition, `/etc/environment` has `RBENV_ROOT` added (`/usr/local/rbenv`) and
`PATH` is updated to include `RBENV_ROOT/{shims,bin,libexec}`.
Finally, `/etc/profile` and `/etc/bash.bashrc` both source `/etc/profile.d/rbenv.sh`

Additional output can be obtained by setting `RBENV_DEBUG`, all installer and
update output is directed to `/var/log/rbenv/system.log`

The skeleton configuration files are not installed to existing user accounts, that is left
a sysadmin/user task.

The `/etc/gemrc` and `/etc/skel/.gemrc` is:

    ---
    :sources:
    - http://gems.rubyforge.org
    - http://gems.github.com
    install: --no-rdoc --no-ri
    update: --no-ri --no-rdoc

The `/etc/skel/.bundle/config` is:

    BUNDLE_BIN: bin
    BUNDLE_SHEBANG: ruby-local-exec
    BUNDLE_DISABLE_SHARED_GEMS: '1'
    BUNDLE_PATH: vendor

Please note, currently, system wide installation of [rbenv] can be different
depending on when in the boot phase you try to install.
Specifically, if trying to install rbenv via a EC2 user-data script, you will
likely have to do the following in your user data script:

    cat <<'EOP' > /tmp/install_rbenv.sh
    #!/bin/bash
    [ -n "$RBENV_DEBUG" ] && set -x
    ## Setup rbenv
    #
    curl -L https://raw.github.com/hedgehog/rbenv-installer/systemwide/bin/rbenv-system-installer | bash
    EOP

    chmod a+x /tmp/install_rbenv.sh
    sudo -i '/tmp/install_rbenv.sh'

Likewise for `rbenv install ...`, `rbenv global ...`, and `rbenv update-alternatives ...`.

That is correct, you can now `update-alternatives` with your rbenv installed Rubies.
Enjoy.

Installing a Ruby
-----------------

Install Ruby `1.9.3-p0` and make it global:

    rbenv install 1.9.3-p0
    rbenv global 1.9.3-p0


Updating
--------

Update `rbenv` and plugins provided by the installer running:

    rbenv update


Bootstrap
---------

If you are installing `rbenv` in Ubuntu you'll probably need to install
required packages first:

    rbenv bootstrap-ubuntu-10-04
    rbenv bootstrap-ubuntu-11-04

When running as `root` (e.g. during a system-wide install), you can use:

    rbenv bootstrap-system-ubuntu-10-04


About rbenv
-----------

**rbenv** source code is available at <https://github.com/sstephenson/rbenv>

[rbenv]: https://github.com/sstephenson/rbenv
[Bundler]: https://github.com/carlhuda/bundler
[ruby_build]: https://github.com/fnichol/chef-ruby_build
[rbenv]: https://github.com/fnichol/chef-rbenv