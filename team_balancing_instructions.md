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
