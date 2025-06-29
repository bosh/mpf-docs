---
title: "logic_blocks: Config Reference"
---

# logic_blocks: Config Reference

Logic blocks moved one level up in MPF 0.50. Instead of

``` yaml
logic_blocks:
  counters:
    your_counter:
      count_events: count_it_up
```

just use:

``` yaml
counters:
  your_counter:
    count_events: count_it_up
```

There are three type of logic blocks:

* [accruals:](accruals.md)
* [counters:](counters.md)
* [sequences:](sequences.md)

Click each of the links above for details and settings for each type of logic block.

## Related How To guides

* [accruals:](../game_logic/logic_blocks/accruals.md)
* [counters:](../game_logic/logic_blocks/counters.md)
* [sequences:](../game_logic/logic_blocks/sequences.md)
