+++
title = "Getting an applications's status"
description = "Reference page for the 'swarm status' command, which prints out the status of a particular application and its services and components."
date = "2015-03-18"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm status"]
weight = 84
+++

# Getting an applications's status

The `swarm` command line tool provides the `status` command for you to fetch information on a specific application as well as its services and components.

## Syntax and output

The command needs the name of your application, which you have to set with the `app_name` key as an argument. Here, `<app_name>` is the name you used in your application configuration file (`swarm.json`) when [creating](/reference/cli/create/) the app. The syntax is:

```nohighlight
$ swarm status [app_name]
```

For example, for an application named "onlineshop", we would use this command:

```nohighlight
$ swarm status onlineshop
```

Here is an example output:

```nohighlight
App onlineshop is up

service      component      image                                          instanceid    created               status
appserver    elasticsearch  registry.giantswarm.io/mycompany/es:latest     TiEjBtvk1L4x  2015-01-06 10:28 UTC  up
appserver    gunicorn       registry.giantswarm.io/mycompany/gunicorn:tag  5Ueplayovegp  2015-01-06 10:28 UTC  up
appserver    gunicorn       registry.giantswarm.io/mycompany/gunicorn:tag  nyMWa7ECI1UC  2015-01-06 10:28 UTC  up
appserver    mongodb        registry.giantswarm.io/mycompany/mongodb:tag   sPZJz6tzRUwL  2015-01-06 10:28 UTC  up
appserver    nginx          registry.giantswarm.io/mycompany/nginx:tag     OIPTDv9MTMfm  2015-01-06 10:28 UTC  up
appserver    redis          redis:latest                                   zQWU5lP3ZWdk  2015-01-06 10:28 UTC  up
imageserver  nginx          nginx:latest                                   xga0mQdQ99mG  2015-01-06 10:28 UTC  up
payments     payments       registry.giantswarm.io/mycompany/payments:0.1  XfZdh7dF6JFb  2015-01-06 10:28 UTC  up
```

The first line of the output shows the status of the application as a summary. This status is an aggregation of the individual component's statuses, with the "worst" status of all components being reported. This means that if even one component is `down`, the entire application is considered `down`, too.

The second part is a table of all components within all services of that application. The table columns show which service the component belongs to, the component name, the ID of the instance the component is running on, the date and time when the instance of the component was first started, and the component status. If a component is running on more than one instance, each instance is represented in an individual row.

## Statuses and their meaning

Your components can have either of the following statuses:

 * `up`: The component is currently running
 * `starting`: The component is currently starting
 * `down`: The component is currently not running
 * `failed`: An error occurred during the attempt to start the component and it's currently not running

## Further reading

* [List applications](/reference/cli/ls/)
* [Accessing process logs](/reference/cli/logs/)
* [Getting instance statistics](/reference/cli/stats/)
* [Getting basic information](/reference/cli/info/)
