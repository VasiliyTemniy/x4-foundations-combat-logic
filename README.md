# Combat Logic

The shared base for VAS combat mods. It does **nothing on its own** - no gameplay
changes, no new orders, no AI behaviour changes. Install it because another mod
lists it as a dependency, and forget about it.

## What it provides

- **Sector scan engine** - sweeps a sector for hostile ships and stations, resolves
  shared faction vision, runs fleet-vs-fleet force comparisons, scores and picks
  targets, and penalizes targets hiding behind enemy force on the route.
- **Attack issuing helper** - turns a picked target into vanilla Attack and movement
  orders, including detour waypoints around hostile stations on the approach.
- **Wake heartbeat** - a global scheduler that periodically signals sectors so
  consumer mods can time their scans. Its pace is driven by the consumer mods'
  own settings; the densest requested interval wins.
- **Garbage collector** - cleans up the short-lived caches the engine uses.

Everything is configured by the consumer mods - this mod has no settings and no
options menu of its own.

## Does it really change nothing?

Almost. It patches two vanilla AI scripts (`order.fight.attack.object`,
`move.generic`) with dormant additions that only activate on orders created by a
consumer mod. Vanilla decision-making is untouched. The patches can still
file-conflict with other mods editing the same two scripts - if something looks
off, check for overlaps there first.

## For modders

The aiscript libraries (`lib.vas.cl.scan`, `lib.vas.cl.issue.attack`) are
config-agnostic: every tunable comes in as a script parameter, results come back
as return values. Consumer mods register themselves through globals:

- `global.$vas_cl_combat_order_ids` - append your order id so your ships count as
  potential combatants in other consumers' force calculations.
- `global.$vas_cl_wake_intervals.{'$<your mod id>'}` - register your desired
  heartbeat interval; sectors are signalled with the `'vas_cl_wake'` param.
  Mind the leading `$` inside the key string - MD table string keys require it,
  and the write silently errors out without it.

## Credits

- Built for **Enhanced Patrol AI** and its sibling mods.
- By VasiliyTemniy.
