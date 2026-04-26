id: 4

title: PX4 #22255 — No-fly zone is not checked for route segments between waypoints (mission-route-crossing-nfz)

description:
Test goal: Verify whether mission feasibility checking only validates waypoint positions and fails to validate whether the straight-line path between waypoints crosses a no-fly zone.

Vulnerability background: PX4 #22255, open/stale. mission_feasibility_checker only checks whether waypoints are inside an exclusion zone and does not validate the straight-line segment between points. Reproduced in SITL.

Scenario steps:
1) Upload an exclusion zone at 80–120 m to the north: [(80,-30),(80,30),(120,30),(120,-30)].
2) Set GF_ACTION=3 (RTL).
3) Upload a mission: WP1=(50,0,15) and WP2=(150,0,15). Both waypoints are outside the no-fly zone, but the connecting line crosses it.
4) Take off in MISSION mode and execute.
5) Observe whether the vehicle flies through the no-fly zone (the 80–120 m north segment).

Expected behavior: The system should detect the route crossing the restricted area and reject the mission or trigger a geofence action during flight.

Abnormal signals:
- Mission upload succeeds and the vehicle flies straight through the no-fly zone.
- No geofence breach warning is raised.

Fields to watch: upload_mission_waypoints return value, current_lat/lon track, STATUSTEXT.

aspect: Mission feasibility check does not validate waypoint-to-waypoint path crossing a no-fly zone

source_scene: PX4 #22255: mission feasibility checker only checks waypoints not route segments
