id: 10

title: PX4 #26670 — DO_CHANGE_ALTITUDE resets the horizontal target (do-change-alt-resets-horizontal)

description:
Test goal: Verify whether sending do_change_altitude during guided flight aborts the horizontal trajectory.

Vulnerability background: PX4 #26670, closed. The multicopter handler calls preproject_stop_point() unconditionally and overwrites the current horizontal navigation target (lat/lon) with a brake point, causing the vehicle to abandon its ongoing horizontal track and loiter/climb in place. A single injected MAVLink packet can interrupt the mission/flight behavior, making it a low-cost DoS vector.

Scenario steps:
1) Take off to 15 m and stabilize in hover.
2) fly_guided_move_local(200, 0, 15) to start flying 200 m north.
3) Wait until the vehicle starts moving (ground_speed > 1 m/s or it has traveled 30 m).
4) Record the position at this moment (pos_before).
5) Call do_change_altitude(20) to change altitude from 15 m to 20 m.
6) Wait 15 seconds and record the position (pos_after).
7) Compute the north displacement from pos_before to pos_after.
8) Compare: if the vehicle continues flying north (normal), the north displacement should be > 30 m.
   If the horizontal target is reset (vulnerable), the vehicle stays near pos_before and the north displacement is < 10 m.

Expected behavior: do_change_altitude should only change altitude and should not affect the horizontal flight target.

Abnormal signals:
- After do_change_altitude, the vehicle stops horizontal movement and ground_speed drops to ~0.
- North displacement is far smaller than expected (< 10 m instead of > 30 m).
- The vehicle climbs to 20 m in place instead of climbing while continuing to fly.

Fields to watch: current_lat/lon track, ground_speed, rel_alt, vertical_speed.

aspect: DO_CHANGE_ALTITUDE aborts the horizontal trajectory

source_scene: PX4 #26670: DO_CHANGE_ALTITUDE handler calls preproject_stop_point overwriting horizontal target
