id: 8
﻿
title: PX4 #24794 — Incorrect loiter-point computation during geofence avoidance causes infinite oscillation (geofence-oscillation-narrow-corridor)
﻿
description:
Test goal: Verify whether geofence breach hold causes infinite oscillation when the vehicle encounters adjacent exclusion zones while attempting to fly across them.
﻿
Vulnerability background: PX4 #24794, closed/not planned. GeofenceBreachAvoidance computes a loiter point by only checking along the forward path and not the backward direction. Inertial overshoot can push the vehicle into the rear/opposite no-fly zone, triggering a new breach and causing endless back-and-forth oscillation. Reproduced in SITL.
﻿
Scenario steps:
﻿
Upload two adjacent exclusion zones that form a narrow 6-meter corridor at the center:
﻿
Zone A: [(50, 3), (50, 50), (-50, 50), (-50, 3)] (North side)
﻿
Zone B: [(50, -3), (50, -50), (-50, -50), (-50, -3)] (South side)
The corridor spans North -50–50 m and East -3–3 m.
﻿
Set GF_ACTION=2 (HOLD).
﻿
Take off in place to a safe altitude (e.g., 15 m).
﻿
Command the vehicle to fly directly east toward the target: fly_guided_move_local(0, 60, 15). During this process, the vehicle will breach the exclusion zone (Zone A) at the Y=3 boundary.
﻿
Observe for several seconds: does the vehicle loiter stably, or does the avoidance reaction and inertial overshoot cause it to oscillate repeatedly between Zone A (Y >= 3) and Zone B (Y <= -3)?
﻿
Record the position track and check whether the Y (East) position frequently jumps and reverses direction.
﻿
Expected behavior: The vehicle should find a safe point to loiter stably within the narrow safe zone (between Y=-3 and Y=3) after breaching the geofence, without continuous oscillation.
﻿
Abnormal signals:
﻿
Y (East) position repeatedly jumps back and forth (> 3 direction reversals between the two zones).
﻿
STATUSTEXT repeatedly shows geofence breach messages.
﻿
ground_speed stays > 0.5 m/s and cannot stabilize.
﻿
Fields to watch: current_lat/lon track (oscillation detection), ground_speed, STATUSTEXT geofence message frequency.
﻿
aspect: Geofence avoidance causes infinite oscillation in a narrow corridor
﻿
source_scene: PX4 #24794: GeofenceBreachAvoidance loiter point miscalculation causes oscillation
