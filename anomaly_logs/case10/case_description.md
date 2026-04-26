id: 9

title: PX4 #27099 — Multiple inclusion fences incorrectly combined with AND logic (multiple-inclusion-fence-and-logic)

description:
Test goal: Verify whether uploading multiple non-overlapping inclusion fences incorrectly shrinks the allowed flight area to their intersection.

Vulnerability background: PX4 #27099, open, PR #27105 proposed. During loading, each inclusion polygon independently checks whether Home is inside; polygons that do not contain Home are dropped. At runtime, isInsidePolygonOrCircle() combines all inclusion fences using &= (AND), requiring the vehicle to be inside all inclusion areas simultaneously rather than any one of them. This shrinks the effective area to the intersection of all inclusion zones and conflicts with MAVLink semantics and ArduPilot behavior.

Scenario steps:
1) Upload two non-overlapping inclusion zones (via upload_multi_polygon_geofence):
   - Zone A (contains Home): [(-60,-60),(-60,60),(60,60),(60,-60)] (60 m square around Home)
   - Zone B (does not contain Home): [(100,-60),(100,60),(220,60),(220,-60)] (north 100–220 m square)
   Both zones are inclusion type and do not overlap.
2) Set GF_ACTION=1 (WARNING) to avoid directly triggering RTL/HOLD.
3) Take off to 15 m and hover near Home (inside Zone A).
4) fly_guided_move_local(150, 0, 15) toward the center of Zone B (north 150 m).
5) Wait 60 seconds and observe whether the vehicle can reach Zone B.
6) Check whether STATUSTEXT reports a geofence breach warning (it should not, because Zone B is also inclusion).

Expected behavior: The vehicle should be able to fly freely between Zone A and Zone B (OR logic: being inside any inclusion zone is allowed).

Abnormal signals:
- Immediately after leaving Zone A, a geofence breach triggers (AND logic: must be inside both zones at once).
- STATUSTEXT shows geofence warnings.
- Zone B is dropped during loading because Home is not inside it, leaving only Zone A active.

Fields to watch: current_lat/lon track, current_flight_mode, STATUSTEXT geofence messages.

aspect: Multiple inclusion fences use AND logic instead of OR

source_scene: PX4 #27099: multiple inclusion fences combined with AND instead of OR
