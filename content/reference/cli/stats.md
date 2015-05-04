+++
title = "Getting statistics for one or several instances"
description = "Reference page for the 'swarm stats' command, which allows you to access basic statistics about the resource usage of your components."
date = "2015-04-20"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm stats"]
weight = 86
+++

# Getting statistics of an application, service, component or instance

The `swarm stats` command allows you to get information on the current system load for an entire application, a service, a component or a specific instance.

## Command syntax

The command requires a selection mechanism as an argument. Depending on what scope you want to display statistics for, you give an application name, a service name, a component name (all three as defined in your [application configuration](/reference/swarm-json/)). To show the statistics for a specific component instance, an instance ID (as fetched via the [`swarm status` command](/reference/cli/status/) is required.

```nohighlight
$ swarm stats <application_name>
$ swarm stats <application_name/service_name>
$ swarm stats <application_name/service_name/component_name>
$ swarm stats <instance_id>
```

## Output

The command will output the statistics of all instances matched by the selection. An example:

```nohighlight
service         component   instanceid        status  memory usage (mb)  memory capacity (mb)  memory usage (%)  cpu usage (%)
content-master  content     hSmPrV0l6BFq5cRq  up      33.53              512                   6.55              0
content-slave   content     WJcSOkDg4mpwAT7B  up      20.71              512                   4.05              0
proxy           proxy       17jzGgU63VlFhJib  up      7.32               512                   1.43              0
proxy           proxy       Inr9L2jfBs5GMrsX  up      7.59               512                   1.48              0
sitesearch      sitesearch  sMEWLDbXnO5xkJY3  up      390.94             512                   76.35             0.17
```

## Further reading

 * [Accessing process logs](/reference/cli/logs/)
 * [Getting an app's status](/reference/cli/status/)
