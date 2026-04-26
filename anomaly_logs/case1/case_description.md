id: 1

title: PX4 #22373 — Home point inside a no-fly zone is not revalidated; RTL returns into the restricted area (unsafe-rtl-to-restricted-home)

description:
Test goal: Verify whether RTL still flies back into a no-fly zone when the Home point is inside an exclusion zone.

Vulnerability background: PX4 #22373, accepted as a bug. The Home position is not revalidated against the geofence, so RTL can navigate back to a Home point located inside a restricted area. Reproduced in SITL and on real hardware.

Scenario steps:
1) Take off to 20 m and hover 100 m north of Home.
2) Upload an exclusion zone covering the original Home location (a 50 m × 50 m exclusion square centered on Home).
3) Set GF_ACTION=1 (WARNING) to avoid the fence itself triggering RTL.
4) Manually switch to RTL mode.
5) Observe whether the vehicle flies back to the Home point inside the no-fly zone and lands.
6) Record the flight track and STATUSTEXT.

Expected behavior: RTL should detect that Home is inside a restricted area and refuse to return there or select a safe alternative.

Abnormal signals:
- The vehicle successfully returns and lands inside the no-fly zone.
- The flight track crosses the exclusion zone boundary.

Fields to watch: current_flight_mode, current_lat/lon track, rel_alt, STATUSTEXT geofence messages.

aspect: RTL does not revalidate Home when Home is inside a no-fly zone

source_scene: PX4 #22373: home point inside no-fly zone not validated before RTL
