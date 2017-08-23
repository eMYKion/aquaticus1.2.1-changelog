# August 2017 changelog for aquaticus1.2.1 and BHV_Defense_Multi

Changed SHORE_IP to be shoreside computer ip in:

	1. launch_mokai.sh
	2. launch_heron.sh

(Change IP back when testing shoreside on a different computer)

In `meta_heron.bhv`, changed all occurences of
`BHV_AdvColregsV2` to `BHV_AdvColregsV17`

In `BHV_Defense_Multi.cpp` in replaces use of `NODE_REPORT` with `NODE_MESSAGE` completely

Created a vehicle struct (`string name`, `string team`, `double nav_x`, `double nav_y`)

`m_opp_list` is a vector of vehicle structs now, not of `NodeReport`(s)

Added `std::vector<struct vehicle> m_all_list` to store (`name`, `team`, `nav_x`, `nav_y`) of all vehicles, is populated by `*_NAV_X` or `*_NAV_Y` information from `NODE_MESSAGE`

Turned `STRICT_ADDRESSING` to `false` in `plug_uFldMessageHandler.moos`

Near the bottom of `BHV_Defense_Multi::onRunState()` Fixed code syntax that previously had two nested for loops with the same iterator name `i` (now `i` and `j`)

A LOT of encapsulation to member functions, especially getting data from `info_buffer`, and managing the attacker. `onRunState()` is now slightly easier to read

from `BHV_Defense_Multi.h`:
	1. Got rid of `m_self`
	2. Got rid of `m_curr_node_report`
	3. Got rid of `m_points`

Got rid of `BHV_Defense_Multi::getOppCoords()`

Heron defending `m_priority` is now based on alphanumeric order (using `std::string::compare()`), rather than sum of character values

Removed redundant or unused header `#include` macros

# Unfixed bugs:

	1. Xbox controller connectivity issues, sometimes doesn't respond mid-game
	2. Red herons aren't moving
	3. Red and blue buttons aren't control toggles, once on, they stay on; meaning you can't unselect one team's mokai and you end up controlling both teams at once if you select another button

Otherwise the defense logic appears to be working when I lead red mokais into the blue field.