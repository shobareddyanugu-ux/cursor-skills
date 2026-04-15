# Team CAN Balancing Exercise

## Purpose
Distribute CANs across teams into balanced groups with roughly equal:
- CAN count
- Renewal goal
- Hosting goal
- At-risk count
- Expected renewal timing

---

## Distribution Rules

| # | Rule | Description |
|---|------|-------------|
| 1 | **Host CAN Grouping** | All CANs with the same host_can must be in the same group |
| 2 | **Mandatory Assignments (Team Andrea only)** | Specific CANs are pre-assigned to named groups (Cameron, Emily, Ben, Justin, Travis, Aaron, Joe) |
| 3 | **At-Risk Balance** | At-risk CANs (cy_at_risk = 1) are evenly distributed across groups first |
| 4 | **Renewal Timing Balance** | CANs are distributed to balance expected renewal dates throughout the year |
| 5 | **Renewal Goal Balance** | Each group should have roughly equal total renewal dollar goals |
| 6 | **Hosting Goal Balance** | Each group should have some hosting goal where possible |
| 7 | **CAN Count Balance** | Each group should have roughly equal number of CANs |

---

## Rule Priority (Highest to Lowest)

| Priority | Rule |
|----------|------|
| 1 | Mandatory Assignments (Team Andrea) |
| 2 | Host CAN Grouping |
| 3 | At-Risk Balance |
| 4 | Renewal Timing Balance |
| 5 | Renewal/Hosting Goal Balance |
| 6 | CAN Count Balance |

---

## Team Group Configuration

| Team | Groups | Group Names |
|------|--------|-------------|
| Team Tracy | 10 | 1-10 |
| Team Andrea | 7 | Cameron (1), Emily (2), Ben (3), Justin (4), Travis (5), Aaron (6), Joe (7) |
| Team Jason | 9 | 1-9 |
| Team Katelyn | 9 | 1-9 |

---

## Team Andrea Mandatory CAN Assignments (Rule 2)

### Cameron (Group 1) - 58 CANs
```
532833, 377953948, 5390, 7157, 9417, 545201, 102512, 862323071, 606927374, 828730446,
284791153, 487509, 523842484, 82399, 68315, 652405, 386015333, 7779453772, 85609, 32539,
17498, 535224, 9575, 2958, 491528, 26963, 597918406, 611496316, 16267, 29975,
16170205, 677664622, 83039, 196390, 32070, 125144, 11929, 322392093, 100395, 54194,
21472, 827892827, 16880, 63565, 814172407, 944819570, 99990, 7997, 66822, 238033036,
7892, 20324, 38634, 36207, 623214, 30530, 28761, 30837890
```

### Emily (Group 2) - 62 CANs
```
263498534, 492663, 109428, 12040, 37522, 597275, 2770, 8430, 1891, 239727233,
7759812091, 239932, 85972, 83312, 680856939, 87081, 82767, 7967, 53738, 675403481,
43052, 61897, 2468, 31089, 279, 11313, 105344, 38356, 84286393, 7784,
36479, 657239, 150670, 25611, 35768, 26395, 51069, 11682, 225198413, 9295,
33960, 27759, 312518161, 130563, 218753521, 9101, 29512, 11550, 27177, 5156532,
17101, 66248, 988413911, 74933, 20236, 20274, 47135, 37143, 35931, 5047, 39451
```

### Ben (Group 3) - 73 CANs
```
412107443, 997538834, 54377, 16663, 607090271, 906960627, 76474, 101774, 5114, 7779413088,
647916444, 5897, 483, 31297, 7724002671, 12342, 69430, 847, 736309440, 536476121,
63186, 10150, 60455, 387190, 100410, 71744, 237026366, 7700606855, 45279, 924335886,
11541, 513766, 68498, 284531, 789492349, 13139, 14958, 47238, 647812, 60403,
3014, 4562, 20366, 11949, 13074, 102891, 643098, 79491, 19067, 7792400474,
16114, 14419, 299529, 11849, 13588, 128403064, 710676571, 148705613, 625178, 160708,
105922512, 712124160, 209864487, 112766979, 526839114, 735114720, 37683, 131330, 984, 349697151,
192849, 466836, 72488691
```

### Justin (Group 4) - 74 CANs
```
66770, 7769671185, 39327, 23078076, 573465, 85686, 7097, 603652488, 98782, 31290,
228378, 131629, 567, 20609, 43590, 4712, 72338, 165740395, 106331, 602774002,
15272, 81491, 72347, 16365, 504140, 26363, 162057, 15955, 591349, 75355,
618933, 168883, 24509, 429133, 61545, 60567, 513439317, 15814, 225564, 54443,
24078, 7702559115, 15422, 2171, 964752508, 543446, 40466, 20914, 52555, 38103,
53569, 19872, 613645, 23650, 486350, 83957, 17445, 209221248, 275945837, 261675873,
675204469, 114062108, 3414, 435736305, 566484, 858779186, 61252, 484337, 216750920, 612682161,
5483, 15956, 15957
```

### Travis (Group 5) - 55 CANs
```
156, 73277, 64418, 26803, 5357, 30461, 72494, 401897889, 86177, 64239,
25038, 926145542, 685147, 594728, 12189, 15717, 819278629, 99522, 39085, 40632,
62770, 97317, 153302, 15196, 596696, 605310, 14092, 1397, 31804, 82596,
14208, 656507, 686435, 3700, 31801, 12718, 4560, 808244338, 17602, 122636,
21201, 536440, 29340, 2114, 45253, 618136, 26419, 27996, 230038, 355174814,
20635, 2445, 28215, 4710, 81531
```

### Aaron (Group 6) - 72 CANs
```
808840064, 495797147, 227012485, 204670662, 1660, 2173, 33492, 17002, 941, 63962,
70197, 71311, 107893, 49911, 122467, 66105, 2225, 48405, 30966, 923,
2172, 530506, 533668, 540960, 130761, 59017, 5946, 127886, 69098, 652842,
105754, 675800987, 516075097, 541507, 11018, 948834664, 323755171, 20153, 767504246, 859792320,
20304, 17819, 16277, 980184035, 1782, 69972, 698284, 3003, 35954, 25647,
82512, 2384618, 34345, 811180146, 13596, 34711, 77503, 42522, 476317911, 97313,
71411, 504671964, 707994524, 591957, 622363071, 7514960716, 607087525, 453688493, 418237521, 867732311,
651422998
```

### Joe (Group 7) - 65 CANs
```
412243662, 37042, 407839097, 27072, 17212, 22083, 69163, 51857, 165097, 1370,
21764, 9185, 612002832, 86631, 631384519, 52180, 64040, 363538, 28801, 283359990,
2967, 28534, 105689408, 462395773, 609406939, 21085, 62023, 20630, 22865, 29145,
73951, 69267, 73412, 151289, 38796, 2937, 484220, 286503980, 72514, 480396139,
100319050, 669999692, 33556, 19673, 6336, 23616, 300816, 16963, 16150, 599121876,
132869, 921341175, 38318, 21100, 4751, 544081, 39500, 51957, 4069, 238413,
6431, 73413, 930893064, 73415, 629542440
```

---

## Data Sources

| Data | Table |
|------|-------|
| CAN, Team, Hosting Goals | `PCG_WS_7216.MAS_MANAGED_ACCOUNT_HOSTING_GOALS_FY26` |
| Renewal Goal, Renewal Date, At-Risk | `pcg_ws.ty26_managed_retention_goals_final` |
| Host CAN Mapping | `pcg_dm.tam_tac_list` (tax_year = '2025') |

---

## Balancing Algorithm

1. **Apply Mandatory Assignments** - For Team Andrea, assign specific CANs to their designated groups first
2. **Group CANs by host_can** - All CANs with the same host_can are treated as a unit
3. **Calculate aggregate metrics** for each host_can group (sum of renewal_goal, hosting_goal, at_risk count)
4. **Prioritize at-risk groups first** - Host_can groups containing at-risk CANs are assigned first
5. **Stratify by renewal month** - For timing balance
6. **Use serpentine ordering** - By combined goal value within each stratum
7. **Round-robin assignment** - Assign host_can groups to team groups

---

## Output Files

| File | Description |
|------|-------------|
| `/Users/sanugu/all_teams_balanced_groups.sql` | SQL query for distribution |
| `/Users/sanugu/all_teams_balanced_groups_v3.csv` | Full CAN-level CSV (38,643 rows) |
| `PCG_WS_7216.all_teams_balanced_groups` | Databricks table |

---

## How to Re-run

1. Share this file with the assistant
2. Ask: "Run the team balancing exercise as described in team_balancing_instructions.md"
3. Update group counts or mandatory assignments if needed
