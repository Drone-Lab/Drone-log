id: 8

title: PX4 #24794 — Incorrect loiter-point computation during geofence avoidance causes infinite oscillation (geofence-oscillation-narrow-corridor)

description:
Test goal: Verify whether geofence breach hold causes infinite oscillation in a narrow corridor formed by two adjacent exclusion zones.

Vulnerability background: PX4 #24794, closed/not planned. GeofenceBreachAvoidance computes a loiter point by only checking along the forward path and not the backward direction. Inertial overshoot can push the vehicle into the rear no-fly zone, triggering a new breach and causing endless back-and-forth oscillation. Reproduced in SITL.

Scenario steps:
1) Upload two adjacent exclusion zones to form a narrow corridor (~30 m wide):
   - Zone A: [(40,-50),(40,-15),(120,-15),(120,-50)] (west side)
   - Zone B: [(40,15),(40,50),(120,50),(120,15)] (east side)
   The corridor spans north 40–120 m and east -15–15 m.
2) Set GF_ACTION=2 (HOLD).
3) Take off to 15 m and fly toward the corridor entrance: fly_guided_move_local(35, 0, 15).
4) Enter the corridor: fly_guided_move_local(80, 0, 15).
5) Observe for 60 seconds: does the vehicle loiter stably, or does it oscillate repeatedly between the two zones?
6) Record the position track and check whether the east position frequently jumps within -15–15 m.

Expected behavior: The vehicle should find a safe point to loiter stably within the corridor, or exit along the corridor.

Abnormal signals:
- East position repeatedly jumps back and forth (> 3 direction reversals).
- STATUSTEXT repeatedly shows geofence breach messages.
- ground_speed stays > 0.5 m/s and cannot stabilize.

Fields to watch: current_lat/lon track (oscillation detection), ground_speed, STATUSTEXT geofence message frequency.

aspect: Geofence avoidance causes infinite oscillation in a narrow corridor

source_scene: PX4 #24794: GeofenceBreachAvoidance loiter point miscalculation causes oscillation
