# August 2017 changelog for aquaticus1.2.1 and BHV_Defense_Multi

The relevant folders for this changelog from moos-ivp-aquaticus inlcude the following:

1. moos-ivp-aquaticus/trunk/missions/aquaticus1.2.1/
1. moos-ivp-aquaticus/trunk/trunk/src/lib_behaviors-aq/

# Major changes

In `BHV_Defense_Multi.cpp`, replaced implementation for `NODE_REPORT` with `NODE_MESSAGE` completely.

Created an app called `pPostMessage`, used in `meta_mokai.moos` so that mokais can easily share their local `NAV_*` information through `NODE_MESSAGE_LOCAL` (see usage below).

`pPostMessage` takes a locally published variable `var_name` and republishes it as `NODE_MESSAGE_LOCAL` as `alias` to `dest_node`. It is a general purpose tool with no parameter assertions.

```
ProcessConfig = pPostMessage
{
  AppTick    = 10
  CommsTick  = 10

  SRC_NODE = $(VNAME)

  //this must be in order!
  MESSAGE = var_name=NAV_X,alias=$(VNAME)_NAV_X,dest_node=all
  MESSAGE = var_name=NAV_Y,alias=$(VNAME)_NAV_Y,dest_node=all
}
```

Got rid of `pNodeReportParse` in `meta_heron.moos`.

Created a `vehicle struct`: 

```c++
struct vehicle{
  std::string name;
  std::string team;
  double nav_x;
  double nav_y;
};
```

`m_opp_list` is a vector of `vehicle struct`s now, not of `NodeReport`(s).

Added `std::vector<struct vehicle> m_all_list` to store all `vehicle`s in game. This is populated by `*_NAV_X` or `*_NAV_Y` information from `NODE_MESSAGE`, and can be "stringified" with `stringifyVehicleVector()`.

In `meta_heron.bhv`, changed all occurences of `BHV_AdvColregsV2` to `BHV_AdvColregsV17`.

Turned `STRICT_ADDRESSING` to `false` in `aquaticus1.2.1/plug_uFldMessageHandler.moos`.

A LOT of encapsulation to member functions, especially getting data from `info_buffer`, and managing the attacker (see list below). `onRunState()` is now much easier to read.
	
1. ```c++ int updateAllList(void)```
1. `int updateNavXY(void)`
1. `int updateTaggedVehicleList(void)`
1. `void findClosestUncoveredAttacker(void)`
1. `void updateOpponentList(void)`
1. `void checkToUnsetAttacker(void)`
1. `void publishNavXY(void)`
1. `int updateCovered(void)`
1. `void setDestination(void)`
1. `void setAngle(void)`

# Minor changes

Variable `ALL_VEHICLES` is posted locally by herons, and is a "stringification" of the `m_all_list` vehicle vector (contains `vehicle struct` information about everyone).

Variable `OPP_VEHICLES` is posted locally by herons, and is a "stringification" of the `m_opp_list` vehicle vector (only contains `vehicle struct` information about enemy team).

Reduced redundancy in and encapsulated vehicle controller logic (see `BHV_Defense_Multi::setDestination()` and `BHV_Defense_Multi::setAngle()`).

Renamed `m_attX` and `m_attY` to `m_enemyFlagX` and `m_enemyFlagY` respectively, (because it stores enemy flag position from param).

Changed iterator names for nested for loops that had previously had the same iterator name as their parent loop.

Replaced every bracketed indexing of a `std::vector` with the rightful `at()`.

Got rid of `BHV_Defense_Multi::getOppCoords()` (moved code sections to member functions above).

Heron `m_priority` defense is now based on "standard english dictionary" order (using `std::string::compare()`), rather than sum of character values.

From `BHV_Defense_Multi.h` and `BHV_Defense_Multi.cpp`:

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

(Change IP back when testing shoreside on a different computer).

Added `pPostMessage` block to `meta_heron.moos`.

Added `ADD_DIRECTORY(pPostMessage)` line to `src/CMakeLists.txt` (be sure to include this if `pPostMessage` cannot be found at build-time).

# Unfixed bugs:

1. Warning: `uProcessWatch` crashes for `red_one`, `red_two`, `blue_one`, `blue_two` in simulation (because process `uSpeechRec` is missing).
1. Warning: `uProcessWatch` crashes for `shoreside` in simulation (because process `pTimeWatch` is missing).
1. Warning: `uFldTagManager` on shoreside gives "Failed VTag Post" and "unhandled mail" warnings. 
1. Xbox controller connectivity issues, sometimes doesn't respond mid-game.
1. Xbox controller doesn't register when plugged into kenya MacOSX (all 4 lights on instead of just player1 light).
1. Red and blue buttons aren't control toggles, once on, they stay on; meaning you can't unselect one team's mokai and you end up controlling both teams at once if you press the other button.

Otherwise the defense logic appears to be working when red mokais are led into the blue field (`MULTI_NOTIFY` indicates an attacker in the `.alog` files), but herons seem to be rarely deploying tag "circles".
