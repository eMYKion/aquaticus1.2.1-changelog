# August 2017 changelog for aquaticus1.2.1 and BHV_Defense_Multi

The relevant folders for this changelog from moos-ivp-aquaticus inlcude:

	1. moos-ivp-aquaticus/trunk/missions/aquaticus1.2.1/
	2. moos-ivp-aquaticus/trunk/trunk/src/lib_behaviors-aq/

# Major changes

In `BHV_Defense_Multi.cpp`, replaced implementation for `NODE_REPORT` with `NODE_MESSAGE` completely.

Created a `vehicle` struct (`string name`, `string team`, `double nav_x`, `double nav_y`).

`m_opp_list` is a vector of vehicle structs now, not of `NodeReport`(s).

Added `std::vector<struct vehicle> m_all_list` to store all `vehicle`s in game. This is populated by `*_NAV_X` or `*_NAV_Y` information from `NODE_MESSAGE`.

In `meta_heron.bhv`, changed all occurences of `BHV_AdvColregsV2` to `BHV_AdvColregsV17`.

Turned `STRICT_ADDRESSING` to `false` in `aquaticus1.2.1/plug_uFldMessageHandler.moos`.

A LOT of encapsulation to member functions, especially getting data from `info_buffer`, and managing the attacker (see list below). `onRunState()` is now slightly easier to read.
	
	1. void updateAllList(void)
	2. int updateNavXY(void)
	3. void updateOpponentList(void)
	4. void checkToUnsetAttacker(void)
	5. void publishNavXY(void)
	6. void updateCovered(void)

# Minor changes

Near the bottom of `BHV_Defense_Multi::onRunState()`, fixed code syntax that previously had two nested for loops with the same iterator name `i` (now `i` and `j`).

Replaced every bracketed indexing of a vector with the rightful `at()`

Got rid of `BHV_Defense_Multi::getOppCoords()` (moved code sections to member functions above).

Heron `m_priority` defense is now based on "standard english dictionary" order (using `std::string::compare()`), rather than sum of character values.

from `BHV_Defense_Multi.h`:

	l. Got rid of `m_self`.
	l. Got rid of `m_curr_node_report`.
	l. Got rid of `m_points`.

Removed redundant or unused header `#include` macros in `BHV_Defense_Multi.cpp` and `BHV_Defense_Multi.h`.

# Configuration changes

Changed SHORE_IP to be shoreside computer ip in:

	1. launch_mokai.sh
	2. launch_heron.sh

(Change IP back when testing shoreside on a different computer)

# Unfixed bugs:

	1. Xbox controller connectivity issues, sometimes doesn't respond mid-game
	2. Red herons aren't moving
	3. Red and blue buttons aren't control toggles, once on, they stay on; meaning you can't unselect one team's mokai and you end up controlling both teams at once if you select another button

Otherwise the defense logic appears to be working when red mokais are led into the blue field.