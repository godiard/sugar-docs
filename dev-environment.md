Setup a development environment
===============================

Sugar is made of several modules and it often depends on libraries
which have not yet been packaged in Linux distributions. To make it
easier for developers to build from sources, we developed a set of
scripts that automates builds and other common development tasks.

Please note: The development environment requires Linux. If you are
running Windows or OS X, you must launch a Linux virtual machine
before continuing.

First of all clone the sugar-build git repository

    git clone git://github.com/sugarlabs/sugar-build.git

Then enter the main directory and pull the source code. 

<pre><code language='sh'>
cd sugar-build
./osbuild pull
</code></pre>

After completing the pull command, you need to build.

<pre><code language='sh'>
./osbuild shell
build
</code></pre>

Finally run sugar.

<pre><code language='sh'>
run
</code></pre>

If anything goes wrong, you can check if there are known problems by looking
at the
[buildbot status](http://buildbot.sugarlabs.org/waterfall). If it's red
then something is wrong and hopefully developers will fix it soon. If it's
green then the issue is probably not yet known and you should report it.

If you are still having problems, read the common issues section or subscribe
to the[sugar-devel](http://lists.sugarlabs.org/listinfo/sugar-devel) mailing
list, and send an email. We will usually reply within 24 hours.

Common Issues
-------------

One common issue is sugar-build failing for people behind **proxies**.  This is
usually marked by an issue with the `volo` command, for example:

    * Building sugar-web

    Command failed: volo -nostamp -f add

    Error: connect ECONNREFUSED

If this issue is affecting to you, you have 2 options.  Preferably, you should
run your first build in an environment without a proxy.  If that is not an
option, you will need to remove the `sugar-web` module.  This means you will
not be able to develop Sugar activities using the web based stack.  To remove
the `sugar-web` module, open `build/modules.json` file and remove the following
lines:

        {
            "has_docs": true,
            "has_checks": true,
            "name": "sugar-web",
            "repo": "https://github.com/sugarlabs/sugar-web.git",
            "clean_stamp": 1
        },

Developing
----------

All the sources are inside the sugar-build directory. You can make
changes using any text editor. If sugar is already running stop it, then
restart it with

<pre><code language='sh'>
./osbuild run
</code></pre>

Once in a while you will want to update to the latest sources, issuing the
command

<pre><code language='sh'>
./osbuild pull
</code></pre>


Commands reference
------------------

You can run the commands as osbuild arguments

<pre><code language='sh'>
./osbuild build
./osbuild run
</code></pre>

Or inside a shell

<pre><code language='sh'>
./osbuild

[sugar-build $] build
[sugar-build $] run
</code></pre>

The following commands are available

#### pull

Pull the latest source code. All modules are pulled unless one is
specified.

#### build

Build the source code. All the modules are built unless one is
specified.

#### run

Run sugar.

#### check

Run tests for sugar-build and all the modules that provides them. It
should usually be run before submitting patches or pushing changes.

#### clean

Delete the build artifacts. If a module is specified only the source
directory of that module is cleaned.

#### shell

Open a shell inside the build environment.


Preferences reference
---------------------

You can set a few options by creating a sugar-build/prefs.json file. For
example

<pre><code language='json'>
{
    "output": "HDMI1",
    "resolution": "1024x768"
}
</code></pre>

The following preferences are available

#### github

You can specify a list of forks and repositories for which you have ssh access,
so that remotes will be setup automatically for those. For example

<pre><code language='json'>
{
    "github": {
        "forks": [
            "myname/sugar.git",
            "myname/sugar-docs.git"
        ],
        "ssh": [
            "sugarlabs/sugar.git",
            "sugarlabs/sugar-docs.git"
        ]
    }
}
</code></pre>

When adding a new module to the lists, you need to delete its directory for
the remotes to be switched over.

#### resolution

This option is only valid when running from X Window. It specifies
the resolution of the window containing the sugar desktop in the
form \[width\]x\[height\]. Otherwise the whole screen is used.

#### output

This option is only valid when running from a text console. Sugar
does not work properly on multiple video outputs, so we need to
select one and turn off the others. By default we select the first
output listed by RandR. You can set this variable to override that
with any other of the connected outputs reported by the xrandr
command, for example VGA1 if you have an external monitor.

### use_broot

Set the option to false if you don't want to build and run in a chroot. If
you don't know what that means you'd better not use the option. This is
currently supported only on Fedora 19, on other distributions it's likely
to fail unless you manually install the required dependencies.

More configuration options
--------------------------

### activities

When you first build the development environment, you will notice that there
are only a few activities installed by default. In Sugar, additional
activities are usually stored in <pre><code
language='sh'>~/Activities</code></pre>
However in the development environment, they are stored in <pre><code
language='sh'>sugar-build/activities</code></pre>
Please note that the sugar-build/activities  often won't get created 
until the user installs or creates an Activity in the sugar-build.
For Installation of an activity please refer to 
[this Manual](http://laptop.org/8.2.0/manual/Sugar_InstallingActivities.html) 
and for the creation of your own Activity, 
you can see the [Activity Creation](http://developer.sugarlabs.org/activity.md.html) page.

Tip for testing and debugging purposes: In order to run multiple versions of the same activity, they need to have different bundle ids, so you have to change the `bundle_id` in `activity.info` file in one of them. For example, I have version 39 of Calculate Activity installed and I want to install the version 41 without removing the installed one. In order to do so, just go to the directory of the v41 and go to 
`Calculate.Activity/activity` and then open the `activity.info` file and change the `bundle_id` of it. After doing that, you will be able to install v41 without removing the previously installed version.

### dotsugar

By default, Sugar stores configuration files, logs, and the datastore
in <pre><code language='sh'>~/.sugar</code></pre>
In the dev-enviroment, these files are stored in <pre><code
language='sh'>sugar-build/home/dotsugar</code></pre>

### gsettings

Sugar uses gsettings for a number of different settings. In the
development environment, these settings are found in <pre><code
language='sh'>sugar-build/home/default/config/dconf</code></pre>

osbuild check uses its own gsetting. These can be found in <pre><code
language='sh'>sugar-build/home/check/config/dconf</code></pre>

Multiple instances
------------------

To run multiple instances of sugar-build, enter the shell and

<pre><code language='sh'>
SUGAR_PROFILE=test1 run &
SUGAR_PROFILE=test2 run &
</code></pre>

Log files
---------

Log files for osbuild are found <pre><code
language='sh'>build/logs</code></pre>

Log files for Sugar and Sugar activities are found in <pre><code
language='sh'>home/dotsugar/default/logs</code></pre>
