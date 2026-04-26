id: 6

title: PX4 #22576 — Changing Home during mission prevents waypoint completion (home-change-breaks-wp-completion)

description:
Test goal: Verify whether changing Home altitude during flight causes waypoint reach checks to fail and makes the vehicle oscillate near a waypoint.

Vulnerability background: PX4 #22576, accepted/stale. In mission_block.cpp, the reach check uses dist_z which depends on the current Home altitude. Changing Home changes dist_z, potentially making the reach condition never pass. Reproduced in SITL.

Scenario steps:
1) Upload a 3-waypoint mission with altitude 15 m: [(80,0,15),(160,0,15),(240,0,15)].
2) Take off in MISSION mode and execute.
3) Wait until near WP1 (about 80 m north, horizontal distance < 10 m).
4) Run change_home_position_rel(0, 0, -5) to lower Home altitude by 5 m.
5) Wait 60 seconds and observe whether current_waypoint advances to the next waypoint.

Expected behavior: After Home is modified, the waypoint reach check should compensate correctly and the mission should continue.

Abnormal signals:
- current_waypoint does not change within 60 seconds (stuck at WP1).
- The vehicle oscillates up and down near the waypoint.

Fields to watch: current_waypoint, rel_alt, current_alt, vertical_speed oscillation.

aspect: Home changes break waypoint completion detection

source_scene: PX4 #22576: home change breaks waypoint completion detection in mission_block.cpp
