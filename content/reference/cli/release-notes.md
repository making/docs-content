+++
title = "Release Notes"
description = "Release notes and changelog for the swarm CLI, showing you what has changed form release to release."
date = "2015-04-20"
type = "page"
weight = 10
+++

# Release Notes

Here we list the more important changes we made between releases.

## Version 0.16.0

Released 2015-04-20

* What was called "company" so far is now called an "organization". The `swarm company` CLI command has been renamed to `swarm org`.
* The `swarm update` command is back and allows to update the docker image run by a component. Check the [reference page](../update/) for details.
* The `swarm stats` command now allows showing the stats for more than one instance. it can be called with an application, service or component name as a selector. Check the [reference page](../stats/) for details.
* The `swarm ls` command can now list applications from all organizations and environments accessible for the current user, using the `--all`/`-a` flag (`swarm ls -a`). Also the command executes faster than before due to parallelization.
* Commands which accept an instance ID as argument (`swarm logs`, `swarm stats`) now also accept partial instance IDs, as long as this part is not ambigious. If, for example, an instance ID is `3nFK77aEF88NnSww`, it will in most cases suffice to call its stats using `swarm stats 3n` or even `swarm stats 3`.
* The `swarm logs` command now defaults to showing the 10 latest rows. Use `-t <n>` to show a specific number of entries or `-t all` to show all entries. See the [reference page](../logs/) for details.
* We now publish SHA1 and MD5 checksums/hashes for our CLI release files. Check the [install reference ](../installation/) for details.


## Version 0.15.0

Released 2015-02-24

* We introduced version checks between CLI and API. Deprecated clients now show an error and prompt the user to install an update whenever the CLI is no longer compatible.
* The CLI now checks once per day for a CLI update and informs the users if a newer version is available.
* Password changes now result in the user being logged out.
* Fixed a problem with continuous log output (`-f` switch in `swarm logs`).


## Version 0.12.0

Released 2015-02-09

* `swarm status` now prints the Docker image used by each component.
* When an error occurs during stopping a component (`swarm stop` or `swarm delete`), this error is displayed to the end user. A common cause for errors when stopping is the improper handling of the stop signal (`SIGTERM`). When you see this kind of error, make sure your containers handle this signal properly.
* The parsing of a local application configuration file (`swarm.json`) has improved so that the use of commands like `swarm status`, `swarm start` and `swarm stop` is possible without using an application name as argument, even when there are variables in the configuration file.
* `swarm info` and `swarm version` now check whether the current CLI version is the latest. In addition, the CLI now sends the CLI version with every request to the API. With this, we are working towards automatic compatibility checks and warnings.
* Port definitions in the application configuration now accept integer values
* Several fixes and improvements of robustness.

## Version 0.10.2

Released 2015-01-13

* We added a simpler, more straight-forward way to define environment variables in the application configuration (`swarm.json`). They can now be set as an object like this: `"env": {"KEY_ONE": "value_one", ...}`. The old Array format will still work, but all documentation has been updated to use the new format.

## Version 0.10.0

Released 2015-01-09

* The `swarm update` has been deactivated due to possible problems and side-effects until we come up with a more stable implementation. Updating components requires the use of `swarm stop` and `swarm start`.

* The `swarm company` command has changed in several ways. `swarm company -l` now lists all companies for the user. `swarm company create <companyname>` creates a new company. Check the [reference page](../companies/) for details.

* The `swarm scaleup` and `swarm scaledown` commands have changed. They can now be used with only a component name as argument and the number `1` will be assumed as default number to scale by. The named argument `--count` can no longer be used.

* `swarm status` no prints the instance creation date for every instance. (Instances created before the release show `n/a` here.)

* The output for `swarm ls` has changed slightly for consistency reasons. The environment is now printed in one column in the common format `<company>/<env>`.

* Fixed a bug in the client where certain passwords where wrongly passed to the API for authentication.

* `swarm help <command>` now prints documentation URLs in some cases. CLI usage texts have been shortened in some cases.

* `swarm user` without argument now prints the currently logged in user.

* Messages printed by some commands in case of errors has been improved.

* New subcommands have been incorporated into the bash completion configuration.

* Changing the email address via `swarm user -u email` no longer requires to enter the old email address.

* `swarm info` no longer shows a "Current company", since there actually is no such thing. There was and is a "Current environment" however, which automatically belongs to a company, as indicated by its name.

* `swarm env <environment-name>` now checks access to the environment (company membership) before setting it as the current one.

* Fixed a bug where the progress indicator in commands like `swarm up` was hidden behind the cursor in some terminals and another bug causing the progress indicator to look "unround".
