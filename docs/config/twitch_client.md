---
title: "twitch_client:"
---

# twitch_client:


--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `twitch_client:` section of your config is where you configure the
built-in Twitch chat monitor.

Before using this plugin you must install the irc library with
`pip3 install irc`

``` yaml
twitch_client:
  user: TwitchBotAccount
  password: oauth:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  channel: ChatChannel
```

``` yaml
machine_vars:
  twitch_user:
    initial_value: 'TwitchBotAccount'
    value_type: str
  twitch_password:
    initial_value: 'oauth:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
    value_type: str
  twitch_channel:
    initial_value: 'ChatChannel'
    value_type: str

twitch_client:
  user_var: twitch_user
  password_var: twitch_password
  channel_var: twitch_channel
```

DO NOT CHECK YOUR PASSWORD INTO SOURCE CONTROL. If you use a service
like Github you should not check in your password. If this is stored
publicly then someone could log in as you on Twitch.

## Optional settings

The following sections are optional in the `twitch_client:` section of
your config. (If you don't include them, the default will be used).

### channel:

Single value, type: `string`. Defaults to empty.

The channel on Twitch which will be monitored for messages.

### channel_var:

Single value, type: `string`. Defaults to empty.

his is the machine variable name that contains the channel on Twitch
which will be monitored for messages.

### password:

Single value, type: `string`. Defaults to empty.

This is a Twitch OAuth token, not the actual password of the user. You
can generate this token at <https://twitchapps.com/tmi/>

### password_var:

Single value, type: `string`. Defaults to empty.

This is the machine variable name that contains the password to use when
connecting to Twitch, This is a Twitch OAuth token, not the actual
password of the user. You can generate this token at
<https://twitchapps.com/tmi/>

When you run `mpf -b` it may show your token in the machine variables
portion of the window. Be careful what you share on stream.

### user:

Single value, type: `string`. Defaults to empty.

This is the user that will connect to Twitch. You may want to create a
separate bot account on Twitch to use for this purpose.

### user_var:

Single value, type: `string`. Defaults to empty.

This is the machine variable name that contains the user that will
connect to Twitch. You may want to create a separate bot account on
Twitch to use for this purpose.

## Related How To guides

--8<-- "todo.md"
