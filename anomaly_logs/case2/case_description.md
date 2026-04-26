id: 2

title: PX4 #22282 — Mission continues while in-flight after dynamically adding a no-fly zone (mission-inside-dynamic-nfz)

description:
Test goal: Verify whether the vehicle continues executing a mission after an in-flight uploaded no-fly zone covers the remaining route.

Vulnerability background: PX4 #22282, accepted. A no-fly zone created during flight does not stop an ongoing mission.

Scenario steps:
1) Upload a 5-waypoint mission: [(50,0,15),(100,0,15),(150,0,15),(200,0,15),(250,0,15)].
2) Set GF_ACTION=3 (RTL).
3) Take off in MISSION mode and start the mission.
4) Wait until reaching waypoint #2 (about 100 m north).
5) Dynamically upload an exclusion zone covering waypoints #3–#5: [(120,-50),(120,50),(280,50),(280,-50)].
6) Observe for 30 seconds: does the vehicle stop the mission / trigger a geofence action, or does it continue into the newly added restricted area?

Expected behavior: A dynamic no-fly zone should take effect immediately and prevent the vehicle from entering.

Abnormal signals:
- The vehicle continues toward waypoint #3 with no geofence messages.
- current_flight_mode remains MISSION.

Fields to watch: current_flight_mode, current_lat/lon track, current_waypoint, STATUSTEXT.

aspect: A no-fly zone created in-flight does not stop an ongoing mission

source_scene: PX4 #22282: dynamically drawn no-fly zone does not stop ongoing mission
