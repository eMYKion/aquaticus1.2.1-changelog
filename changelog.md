# August 2017 changelog for aquaticus1.2.1 and BHV_Defense_Multi

The relevant folders for this changelog from moos-ivp-aquaticus inlcude:

1. moos-ivp-aquaticus/trunk/missions/aquaticus1.2.1/
1. moos-ivp-aquaticus/trunk/trunk/src/lib_behaviors-aq/

# Major changes

In `BHV_Defense_Multi.cpp`, replaced implementation for `NODE_REPORT` with `NODE_MESSAGE` completely.

Created an app called `pPostMessage`, used in `meta_mokai.moos` so that mokais can easily share their NAV_* information through NODE_MESSAGE_LOCAL (see usage below).

`pPostMessage` takes a locally published variable `var_name` and republishes it as `NODE_MESSAGE_LOCAL` as `alias` to `dest_node`

```
ProcessConfig = pPostMessage
{
  AppTick    = 10
  CommsTick  = 10

  SRC_NODE = $(VNAME)

  MESSAGE = var_name=NAV_X,alias=$(VNAME)_NAV_X,dest_node=all
  MESSAGE = var_name=NAV_Y,alias=$(VNAME)_NAV_Y,dest_node=all
}
```

Got rid of `pNodeReportParse` in `meta_heron.moos`.

Created a `vehicle` struct (`std::string name`, `std::string team`, `double nav_x`, `double nav_y`).

`m_opp_list` is a vector of vehicle structs now, not of `NodeReport`(s).

Added `std::vector<struct vehicle> m_all_list` to store all `vehicle`s in game. This is populated by `*_NAV_X` or `*_NAV_Y` information from `NODE_MESSAGE`.

In `meta_heron.bhv`, changed all occurences of `BHV_AdvColregsV2` to `BHV_AdvColregsV17`.

Turned `STRICT_ADDRESSING` to `false` in `aquaticus1.2.1/plug_uFldMessageHandler.moos`.

A LOT of encapsulation to member functions, especially getting data from `info_buffer`, and managing the attacker (see list below). `onRunState()` is now easier to read.
	
1. `int updateAllList(void)`
1. `int updateNavXY(void)`
1. `int updateTaggedVehicleList()`
1. `void findClosestUncoveredAttacker(void)`
1. `void updateOpponentList(void)`
1. `void checkToUnsetAttacker(void)`
1. `void publishNavXY(void)`
1. `int updateCovered(void)`
1. `void setDestination(void)`
1. `void setAngle(void)`

# Minor changes

Reduced redundancy in and encapsulated vehicle controller logic (see `BHV_Defense_Multi::setDestination()` and `BHV_Defense_Multi::setAngle()`).

Renamed `m_attX` and `m_attY` to `m_enemyFlagX` and `m_enemyFlagY` respectively, (because it stores enemy flag position from param).

Near the bottom of `BHV_Defense_Multi::onRunState()`, fixed code syntax that previously had two nested for loops with the same iterator name `i` (now `i` and `j`).

Replaced every bracketed indexing of a `std::vector` with the rightful `at()`

Got rid of `BHV_Defense_Multi::getOppCoords()` (moved code sections to member functions above).

Heron `m_priority` defense is now based on "standard english dictionary" order (using `std::string::compare()`), rather than sum of character values.

from `BHV_Defense_Multi.h` and `BHV_Defense_Multi.cpp`:

1. Got rid of `m_self`.
1. Got rid of `m_curr_node_report`.
1. Got rid of `m_points`.

Got rid of undocumented and unused `flag_x` param condition in `BHV_Defense_Multi.cpp`.

Removed redundant or unused header `#include` macros in `BHV_Defense_Multi.cpp` and `BHV_Defense_Multi.h`.

Spacing and tab indentation.

# Configuration changes

Changed SHORE_IP to be shoreside computer ip in:

1. `launch_mokai.sh`
1. `launch_heron.sh`

(Change IP back when testing shoreside on a different computer)

# Unfixed bugs:

1. `m_attacker` is always `"none"` for any given game...
1. `uProcessWatch` crashes for `red_one`, `red_two`, `blue_one`, `blue_two` in simulation (because process `uSpeechRec` is missing).
1. `uProcessWatch` crashes for `shoreside` in simulation (because process `pTimeWatch` is missing).
1. Xbox controller connectivity issues, sometimes doesn't respond mid-game
1. xbox controller doesn't register when plugged into kenya MacOSX (all 4 lights on instead of just player1 light)
1. Red herons aren't moving; have MOOS manual override switched on the entire game
1. Red and blue buttons aren't control toggles, once on, they stay on; meaning you can't unselect one team's mokai and you end up controlling both teams at once if you press another button

Otherwise the defense logic appears to be working when red mokais are led into the blue field, but herons seem to be rarely deploying tag "circles".