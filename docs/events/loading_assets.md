---
title: loading_assets
---

# loading_assets


--8<-- "event.md"

Posted when the number of assets waiting to be loaded changes.

Note that once all the assets are loaded, all the values below are reset
to zero.

## Keyword arguments

(See the [Conditional Events](overview/conditional.md)
guide for details for how to create entries in your config file that
only respond to certain combinations of the arguments below.)

#### `loaded`:

The number of assets that have been loaded so far.

#### `percent`:

The numerical percent completion of the assets loaded, express in
the range of 0 to 100.

#### `remaining`:

The number of assets that are remaining to be loaded.

#### `total`:

The total number of assets that need to be loaded. This is equal to
the sum of the *loaded* and *remaining* values below. It also
includes assets that MPF is loading itself as well as any assets
that have been reported from remotely connected BCP hosts (e.g. the
media controller).
