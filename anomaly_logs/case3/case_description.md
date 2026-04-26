id: 3

title: PX4 #22362 — Race condition bypasses mission–geofence pre-check (race-condition-mission-geofence-check)

description:
Test goal: Verify whether a race condition causes the feasibility checker to use stale fence data when uploading a mission and a geofence at the same time.

Vulnerability background: PX4 #22362 / GHSA-p74g-gvg5-6pgc, CVSS 8.1. Geofence data loads asynchronously while MissionFeasibilityChecker reads synchronously; when both are uploaded in quick succession, the checker may pass using old data.

Scenario steps:
1) Take off and hover at 15 m.
2) Upload a 100 m inclusion zone: [(-100,-100),(-100,100),(100,100),(100,-100)], GF_ACTION=RETURN.
3) Execute the following quickly (minimize any gap):
   a) Upload a new 50 m inclusion zone: [(-50,-50),(-50,50),(50,50),(50,-50)].
   b) Immediately upload a mission with waypoints at 75 m: [(75,0,15),(80,30,15)].
4) Switch to MISSION mode and observe whether the mission is accepted and executed.

Expected behavior: The latest 50 m fence should be used to reject waypoints located at 75 m.

Abnormal signals:
- The mission is accepted and the vehicle flies toward 75 m.
- No feasibility-check failure message is shown.

Fields to watch: upload_mission_waypoints return value, current_flight_mode, current_waypoint, STATUSTEXT.

aspect: Race condition bypasses the pre-check when uploading mission and fence concurrently

source_scene: PX4 #22362: race between geofence async load and mission feasibility check
