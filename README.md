# POD - super simple Node.js deployment tool

**WARNING:** test suite not complete, use with caution!

Pod simplifies the workflow of setting up, updating and managing multiple Node.js apps on a single Linux server. Perfect for experimenting with Node stuff on a VPS. It is built upon git hooks and [forever](https://github.com/nodejitsu/forever).

It doesn't manage DNS routing for you (personally I'm doing that in Nginx) but you can use pod to run a [node-http-proxy](https://github.com/nodejitsu/node-http-proxy) server on port 80 that routes incoming requests to other apps.

## Prerequisites

All you need is Node, git, and a ssh account.

## Example Workflow

**On the server:**

``` bash
$ pod create myapp
# will print path to repo and working tree
```

**On your local machine:**

``` bash
$ git clone ssh://your-server/pod_dir/myapp.git
# hack hack hack, commit
$ git push
```

**Or use an existing local repo:**

``` bash
$ git remote add deploy ssh://your-server/pod_dir/myapp.git
$ git push deploy master
```

That's it! App should be automatically running after the push. For later pushes, app process will be restarted.  

You can edit the post-receive script of an app using `pod edit <appname>` to customize the actions after a git push.

## Installation

``` bash
$ [sudo] npm install -g pod
```

To make pod auto start all managed apps on system startup, you might also want write a simple [upstart](http://upstart.ubuntu.com) script that contains something like this:

``` bash
# /etc/init/pod.conf
start on startup
exec sudo -u <username> /path/to/node /path/to/pod startall
```

The first time you run `pod` it will ask you where you want to put your stuff. The structure of the given directory will look like this:

``` bash
.
├── repos # holds the bare .git repos
│   └── example.git
├── apps # holds the working copies
│   └── example
│       └──app.js
└── logs # holds the logs
    └── example
        ├── forever.log
        ├── stdout.log
        └── stderr.log
```

## Usage

```

  Usage: pod [command]

  Commands:

    create <app>            Create a new app
    rm <app>                Delete an app
    cleanlogs <app>         Clean up logs for an app
    edit <app>              Edit the app's post-receive hook
    list [-p]               List apps and status. [-p] = List processes
    start <app>             Start an app with forever
    stop <app>              Stop an app
    restart <app>           Restart an app, start if not already running
    startall                Start all apps not already running
    stopall                 Stop all apps
    restartall              Restart all running apps
    help                    You are reading it right now
    config [app]            Edit global or app configs

```