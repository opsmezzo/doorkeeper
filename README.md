# doorkeeper
SSH login based on keys from composer.

## How it works?
OpenSSH 6.2 introduced `AuthorizedKeysCommand` `sshd_config` option, which
allows ssh daemon to invoke a command each time a login is attempted to fetch
keys for the specific username. This is essentially how `doorkeeper` works - it
fetches keys from `composer` service to find out which users should be allowed
to SSH onto this machine.

*Note: it doesn't support per-user authentication. All keys added to `composer` are valid.*

## How to set it up?
Put `bin/doorkeeper` somewhere (`npm -g install` might not work great due to
permission issues) and add those 2 lines to your `/etc/ssh/sshd_config`:

```
AuthorizedKeysCommand <path-to-doorkeeper>
AuthorizedKeysCommandUser <user-you-want-doorkeeper-to-run-as>
```

Example from nodejitsu server:
```
AuthorizedKeysCommand /usr/bin/doorkeeper
AuthorizedKeysCommandUser root
```

`doorkeeper` needs a config file to know where `composer` service is running.
It looks up 3 paths: `/etc/doorkeeper`, `$HOME/.quillconf`, `/root/.quillconf`.

Example config file:

```json
{
  "remoteHost": "127.0.0.1",
  "port": 9003
}
```

If no port is given, it defaults to 9003.

Please note that `doorkeeper`'s config file format is identical to `quill`'s,
so if you have `quill` installed and configured, `doorkeeper` integration is
seamless.
