# August 2017 changelog for aquaticus1.2.1 and BHV_Defense_Multi

The relevant folders for this changelog from moos-ivp-aquaticus inlcude:

1. moos-ivp-aquaticus/trunk/missions/aquaticus1.2.1/
1. moos-ivp-aquaticus/trunk/trunk/src/lib_behaviors-aq/

# Major changes

In `BHV_Defense_Multi.cpp`, replaced implementation for `NODE_REPORT` with `NODE_MESSAGE` completely.

Created a `vehicle` struct (`std::string name`, `std::string team`, `double nav_x`, `double nav_y`).

`m_opp_list` is a vector of vehicle structs now, not of `NodeReport`(s).

Added `std::vector<struct vehicle> m_all_list` to store all `vehicle`s in game. This is populated by `*_NAV_X` or `*_NAV_Y` information from `NODE_MESSAGE`.

In `meta_heron.bhv`, changed all occurences of `BHV_AdvColregsV2` to `BHV_AdvColregsV17`.

Turned `STRICT_ADDRESSING` to `false` in `aquaticus1.2.1/plug_uFldMessageHandler.moos`.

A LOT of encapsulation to member functions, especially getting data from `info_buffer`, and managing the attacker (see list below). `onRunState()` is now slightly easier to read.
	
1. `int updateAllList(void)`
1. `int updateNavXY(void)`
1. `void updateOpponentList(void)`
1. `void checkToUnsetAttacker(void)`
1. `void publishNavXY(void)`
1. `int updateCovered(void)`

# Minor changes

Near the bottom of `BHV_Defense_Multi::onRunState()`, fixed code syntax that previously had two nested for loops with the same iterator name `i` (now `i` and `j`).

Replaced every bracketed indexing of a `std::vector` with the rightful `at()`

Got rid of `BHV_Defense_Multi::getOppCoords()` (moved code sections to member functions above).

Heron `m_priority` defense is now based on "standard english dictionary" order (using `std::string::compare()`), rather than sum of character values.

from `BHV_Defense_Multi.h` and `BHV_Defense_Multi.cpp`:

1. Got rid of `m_self`.
1. Got rid of `m_curr_node_report`.
1. Got rid of `m_points`.

Removed redundant or unused header `#include` macros in `BHV_Defense_Multi.cpp` and `BHV_Defense_Multi.h`.

Spacing and tab indentation.

# Configuration changes

Changed SHORE_IP to be shoreside computer ip in:

1. `launch_mokai.sh`
1. `launch_heron.sh`

(Change IP back when testing shoreside on a different computer)

# Unfixed bugs:

1. Xbox controller connectivity issues, sometimes doesn't respond mid-game
1. xbox controller doesn't register when plugged into kenya MacOSX (all 4 lights on instead of just player1 light)
1. Red herons aren't moving; have MOOS manual override switched on the entire game
1. Red and blue buttons aren't control toggles, once on, they stay on; meaning you can't unselect one team's mokai and you end up controlling both teams at once if you press another button

Otherwise the defense logic appears to be working when red mokais are led into the blue field, but herons seem to be rarely deploying tag "circles".