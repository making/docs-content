+++
title = "Accessing process logs"
description = "This is the reference page for the 'swarm logs' command, which allows you to access the logs of your component instances."
date = "2015-04-20"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm logs"]
weight = 87
+++

# Accessing process logs

Logs of processes running on Giant Swarm can be accessed using the `swarm logs` command.

The command requires an instance ID for the instance your component is running on as an argument. An instance ID can be found using the [`swarm status`](/reference/cli/status/) command. If there is more than one instance running for the component you are interested in, you have to inquire the logs for each instance seperately.

Notes:

* For logs to be accessible in this way, processes have to send their log messages to STDOUT or STDERR. This is the standard with Docker containers running single processes each.
* Log entries are stored for a couple of days only, for the time being.

## Return the latest log messages

To quickly return the latest 10 log entries for an instance, simply give the instance ID as an argument:

```nohighlight
$ swarm logs <instance_id>
```

Or

```nohighlight
$ swarm logs <instance_id>
```

## Returning more log messages

To adjust the number of log messages to be returned from the end, use the `--tail` or `-t` parameter and use it to set the number of entries to return. The syntax is like this:

```nohighlight
$ swarm logs <instance_id> -t <n>
```

As a result, the latest `n` entries recorded so far for this instance will be printed to your console.

To return all stored log entries for that instance, the special value `all` is accepted, like this:

```nohighlight
$ swarm logs <instance_id> -t all
```

## Continuous output

You can print out new log messages as they occur. For this purpose, add the `--follow` or `-f` switch to the command.

```nohighlight
$ swarm logs <instance_id> -f
```

Or

```nohighlight
$ swarm logs <instance_id> --follow
```

You can also combine the `--tail`/`-t` and the `--follow`/`-f` switches to first cap the log output to a specific number of rows and then follow new messages as they come up. An example:

```nohighlight
$ swarm logs AfeLfIT1SeYy -t 100 -f
```

## Further reading

 * [Getting an application's status](/reference/cli/status/)
 * [Getting statistics](/reference/cli/stats/)
