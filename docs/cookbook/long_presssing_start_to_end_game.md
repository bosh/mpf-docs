---
title: Ending the Current Game by Long-pressing Start
---

# Ending the Current Game by Long-pressing Start


Related Config File Sections:

* [timed_switches:](../config/timed_switches.md)

The following snippet will end a running game by long-pressing the start
button:

``` yaml
timed_switches:
  game_cancel:
    switch_tags: start
    time: 5s
    events_when_active: end_game
```

Please note that this will also work on ball one and will not inhibit
bonus nor high_score mode. Let us know in the forum if you need this.
