+++
title = "Logging in"
description = "Reference page for the `swarm login` command, which allows you to authenticate with your Giant Swarm credentials."
date = "2015-03-19"
type = "page"
categories = ["Reference", "Swarm CLI Commands"]
tags = ["swarm login"]
weight = 40
+++

# Logging in using the CLI

The `swarm login` command allows you to authenticate with the <abbr title="command line interface">CLI</appr>.

The command can be used in an interactive way, where the credentials are entered interactively. Alternatively, credentials can be passed via the command line to enable non-interactive use.

## Notice on authenticated sessions

When logging in using the `swarm login` command, an authenticated session is opened that will be valid for up to 30 days. During that period, no further authentication is required.

To minimize the risk of unauthorized use, please use the `swarm logout` command to explicitly quit the authenticated session.

## Interactive login

The command can be called without any arguments to invoke the interactive login:

```nohighlight
$ swarm login
```

You will then be prompted for your Giant Swarm username or, alternatively, your email address. When using your email address, please double-check that you are using exactly the address you used when creating your user account.

After you confirmed the username/email input, you are prompted for the password. The entry of the password won't be visible.

After the successfull login, an output like this should be shown:

```nohighlight
Login Succeeded
Environment yourusername/dev has been selected
```

The last output line informs you which [environment](/reference/cli/env/) has been selected as the current environment. It defaults to `<yourusername>/dev`.

## Non-interactive login

Both username and password can be passed to the login command as arguments. Here is an example:

```nohighlight
$ swarm login yourusername -p secretpassword
```

To pass the username, but not the password as an argument, it's possible to use the following syntax. In this case you will be prompted for the password only:

```nohighlight
$ swarm login yourusername
```

## Further reading

* [Environments](/reference/cli/env/)
