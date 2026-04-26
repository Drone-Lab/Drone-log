id: 5

title: PX4 #22491 — Changing Home-relative altitude causes in-flight crash (home-alt-change-crash)

description:
Test goal: Verify whether lowering Home altitude during flight causes mission altitude recomputation that triggers an unexpected descent or crash.

Vulnerability background: PX4 #22491, accepted/stale. Mission altitudes are computed relative to Home; lowering Home makes the vehicle reinterpret targets as a lower absolute altitude. If the delta exceeds the mission altitude, the vehicle can hit the ground. Reproduced in SITL.

Scenario steps:
1) Upload a mission with altitude 10 m: [(100,0,10),(200,0,10)].
2) Take off in MISSION mode and execute.
3) Wait until reaching WP1 (100 m north, 10 m altitude).
4) Run change_home_position_rel(0, 0, -15) to lower Home altitude by 15 m.
   At this point, mission 10 m relative to the new Home equals the original absolute altitude minus 5 m (below ground).
5) Monitor altitude; if rel_alt < 3 m, manually switch to LAND to recover.

Expected behavior: Changing Home altitude should not cause the mission to be recomputed to an unsafe altitude.

Abnormal signals:
- The vehicle descends immediately after Home is modified.
- current_alt (AMSL) drops sharply and armed becomes false.

Fields to watch: rel_alt, current_alt (AMSL), vertical_speed, armed, STATUSTEXT.

aspect: Changing Home altitude in-flight causes mission altitude recomputation leading to crash

source_scene: PX4 #22491: home altitude modification causes unexpected descent/crash
