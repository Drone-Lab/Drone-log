id: 7

title: PX4 #22492 — Mission Pause command ignored (race condition) (mission-pause-ignored-race)

description:
Test goal: Verify whether a Pause command during mission execution fails to stop the vehicle due to a race condition.

Vulnerability background: PX4 #22492 / GHSA-x57h-c282-63hg, CVSS 8.2 (fixed by PR #22651). A race between commander.cpp and navigator_main.cpp can overwrite the brake point; the vehicle enters hold/loiter but continues toward the next waypoint. CWE-362.

Scenario steps:
1) Upload a long-distance waypoint mission: [(200,0,15),(400,0,15),(600,0,15)].
2) Take off in MISSION mode and execute.
3) Wait until the vehicle starts flying toward WP1 (current_waypoint >= 1).
4) When it is about 100 m north, call do_pause() to pause.
5) Record the position at the moment of Pause.
6) Wait 15 seconds and observe whether the vehicle stays at the Pause position or keeps moving forward.
7) Compute the position drift after 15 seconds relative to the Pause position (it must be < 5 m to count as a real pause).

Expected behavior: After Pause, the vehicle should stop immediately and loiter at the current position.

Abnormal signals:
- After Pause, the vehicle continues moving toward WP1 (drift > 10 m).
- current_flight_mode shows LOITER but the position keeps changing.

Fields to watch: current_flight_mode, current_lat/lon (position drift before/after Pause), ground_speed.

aspect: Mission Pause command is ineffective due to a race condition

source_scene: PX4 #22492: race condition between commander and navigator on pause/brake point
