---
title: "bcp_connection: Config Reference"
---

# bcp_connection: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**NO** :no_entry_sign:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `connections:` setting in your `bcp:` section of your config is
where you configure BCP connections MPF should establish on startup.

## Required settings

The following sections are required in the `bcp_connection:` section of
your config:

### type:

Single value, type: `string`.

The class to implement the transport. Use
`mpf.core.bcp.bcp_socket_client.BCPClientSocket` to use the standard MPF
BCP protocol.

More implementations are possible here. For instance, a highly efficient
implemententation for production or an encrypted socket for
communication over the Internet.

## Optional settings

The following sections are optional in the `bcp_connection:` section of
your config. (If you don't include them, the default will be used).

### exit_on_close:

Single value, type: `boolean` (Yes/No or True/False). Default: `True`

Whatever MPF should exit if this connection disconnects. This is usually
true for the media manager because we want MPF to exit once it is
closed.

### host:

Single value, type: `string`.

The host to connect to for this connection.

### port:

Single value, type: `integer`. Default: `5050`

The port to connect to for this connection.

### required:

Single value, type: `boolean` (Yes/No or True/False). Default: `True`

Whatever this connection is required for MPF to run. Set this to false
if you want MPF not to wait for this connection on start.

## Related How To guides

* [The MPF Unity BCP Server](../mc/unity_bcp_server.md)
* [Creating your own Media Controller](../mc/creating_your_own.md)
