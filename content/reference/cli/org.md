+++
title = "Organizations"
description = "This is the reference page for the 'swarm org' command, which allows you to manage teams of users."
date = "2015-01-08"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm org"]
weight = 100
+++

# Organizations

Organizations allow for sharing resources between users. Users belonging to the same organization can, for example, control applications of that organization or access the organization's docker images on our [registry](/reference/registry/).

## Your default organization

As a Giant Swarm user you automatically have a _default organization_ assigned to you. This is named after your username. So if your username is `yourname`, your default organization name is `yourname`, too.

This is especially relevant for two reasons:

* All your applications on Giant Swarm are associated with a distinct [environment](/reference/cli/env/). When you first login with the `swarm` CLI as user `yourname`, an environment is automatically created and selected. This default environment is called `yourname/dev` and belongs to your default organization.

* When using our [private registry](/reference/registry/) for your Docker images, organization names are used as namespace identifiers. When starting with Giant Swarm, simply use your default organization name (your username) as image namespace.

## Creating an organization

To create an organization, call `swarm org create <organization-name>`:

```nohighlight
$ swarm org create mygreatorganization
Organization mygreatorganization has been created
```

Note that organization names are unique within Giant Swarm, so if the organization has been already created, additional users have to be [added](#adding-a-user-to-an-organization) by the initial creator of the organization.

## Listing organization membership

As a user you can be part of any number of organizations. To list the organizations you belong to, use the `-l` switch:

```nohighlight
$ swarm org -l
giantswarm
luebken
mygreatorganization
```

## Adding a user to an organization

In order to add a user as a member of a certain existing organization, a member of that organization can issue a `swarm org add-user` command with the organization name and the username as arguments. The general syntax is like this:

```nohighlight
$ swarm org add-user <organization> <user>
```

Example:

```nohighlight
$ swarm org add-user myorganization myuser
```

## Removing a user from an organization

To end a user's organization membership, use the `remove-user` subcommand. The syntax is pretty much the same as with the `add-user` subcommand:

```nohighlight
$ swarm org remove-user <organization> <username>
```

## Show organization members

To find out who is member of an organization, use the `show` subcommand with this syntax:

```nohighlight
$ swarm org show <organization>
```

Example:

```nohighlight
$ swarm org show giants
Organization giants

Username:
mel.hein
frank.gifford
ya.tittle
lawrence.taylor
```

<!-- TODO: Deleting an organization (cannot yet explain this well) -->

## Using organizations

As the member of an organization, you can control all existing applications and create new applications within any environment of that organization. Have a look at the [environments](/reference/cli/env/) reference for details.

You can also push images to the namespaces of any of your organizations in the private registry and use all images with your organizations namespaces in your applications. Please refer to the [registry reference](/reference/registry/) for further information.

## Further Reading

* [Environments](/reference/cli/env/)
* [Using the registry](/reference/registry/)
