# Changelog

All notable changes to Virtual Workspace Monitor. The same list is shown in the app under Settings > Changelog.

## 1.6.0 — 2026-11-16

- Added — DEM Profile Health page: scans each user's FlexEngine.log (sync + async) and optionally the user's profile Archive folder; shows logon errors, slow sections, profile size per user and trends over time
- Added — Direct SMB to the DEM share via smbprotocol (bypasses Windows' SMB client, resolves error 1312 on servers with a strict SMB policy); Settings > DEM > Share connection > Advanced offers toggles for Direct SMB and Signing, with graceful fallback to the legacy route if smbprotocol is not bundled
- Added — Test credentials in Share connection now shows the negotiated SMB dialect, encryption cipher and signing requirement, so you can verify at a glance what level the server enforces
- Changed — machine popup session chart defaults to 1h at real-time (20-second) resolution, matching vCenter's Real-time view, instead of 5-minute averages; a 1h/8h toggle switches between real-time and the longer, averaged view
- Changed — build process now uses a self-compiled PyInstaller bootloader, disables UPX compression and embeds publisher/product/copyright metadata plus a VWM icon — reduces generic antivirus false-positive detections on the .exe
- Fixed — logon time now shows the perceived logon (profile, GPO, shell, agent steps) instead of Horizon's wall-clock total, so a slow client or a cold instant clone no longer looks like a multi-minute logon; a one-time migration recalculates old values
- Fixed — Pool sizing read the live clone count instead of the configured maximum, so instant-clone pools showed too few machines; it now reads max_number_of_machines
- Fixed — Top Users disconnected could stay empty and now also counts currently-disconnected (live) sessions, not only ones closed in the session log
- Fixed — the session performance chart put network (Mbit/s) at the same height as CPU/memory %, making low traffic look like 100%; network now has its own labelled right axis
- Fixed — the audit log recorded a double entry for a double-submitted login; identical actions within a few seconds are now logged once
- Fixed — snooze and maintenance mode now also suppress the alert-engine e-mails; External vs Internal per pool has a legend; the per-pool daily breakdown covers the full period again
- Added — large-environment support: higher timeouts, pagination and caching so thousands of machines no longer stall the monitor; a simulation mode (--simulate) to test against
- Added — Audit Log tab: the monitor's own audit trail (logins, config changes, user management) with user, IP, filters and CSV export (admins only)
- Added — Settings > System Health: a readiness check (what works with the configured accounts and what to fix), monitor self-stats (memory, file sizes, disk free) and last poll-cycle duration
- Added — a daily log file (logs\vwm-YYYY-MM-DD.log) and a Support export with masking; configurable listen port (Settings, config.json or --port)
- Changed — console, log file, API docs and e-mail reports are fully in English
- Added — mandatory two-factor authentication, set separately for local and Active Directory accounts; users without 2FA enrol at next login (QR generated locally, no external service), with recovery codes, per-user reset and a command-line break-glass reset
- Added — Clear buttons for stored secrets (SMTP, Kemp API key, Teams/Slack webhooks)
- Changed — Charts renamed Live Charts and moved to the Live section; Pool Usage moved to History; the datastore-capacity and licence-usage bars were removed from the dashboard (they said little for instant clones / named-user licensing)
- Added — click a machine, pool or user name in a session/machine/pool popup to jump straight to that detail, with a Back link; detail popups can be dragged and resized
- Added — At a Glance flags offline Connection Servers / UAG gateways and shows a recent-change line so cause sits next to effect
- Changed — Pools tab: each pool is its own card with search, filter and sort (name, busiest, worst score, largest); healthy pools are no longer tinted, only ones needing attention
- Added — a dashboard / kiosk mode (fullscreen, stays signed in, auto-refresh) that is read-only via the viewer role — suitable for a NOC/SOC wall display
- Added — History: logon time per pool (golden-image markers and phase breakdown), datastore usage over time with a reaches-90% estimate, sessions per Connection Server / UAG over time, and a Changes card (pool size, power policy, enable/disable, CS/UAG versions)
- Added — Maintenance windows as their own History card: best window on top, then a 24-hour-per-weekday strip (green quiet, red busy)
- Added — User Activity: unique-user counts over 30 / 90 / 365 days for named-user licence audits; Export daily CSV (whole daily archive, one row per pool per day); optional Dashboard URL for deep links in alert e-mails
- Added — Top Users — disconnected (who leaves sessions disconnected most often), with a per-user detail popup; Show all on the Top Users cards and consistent user avatars throughout
- Added — machine/session popup: a network tile and a CPU/memory/network timeline over the session from vCenter, with machine and session actions matching Horizon Console (Recover, Maintenance, Restart, Reset, Rebuild, Remove) and the connected duration in the status tile
- Added — a visual HTML report over a period (last month / quarter / 30d / 90d / custom) with a session-trend chart, availability, pool capacity, licence, infrastructure, top users, changes and alerts, in your branding, with selectable sections
- Added — Slack webhook (critical alerts, alongside Teams) and a read-only /api/prtg metrics endpoint in PRTG custom-sensor JSON, also usable by Grafana/Zabbix
- Added — an opt-in update check against GitHub with its own Version & updates tab, and the version number on the login screen
- Added — load-balancer monitoring (Kemp): a new alert when it is unreachable or a virtual service is down; snooze covers vCenter alarms and the load balancer; a new alert type for red vCenter alarms
- Fixed — a failed sign-in (wrong username or password) is now logged as "failed" in the audit log instead of "ok", so failed login attempts are visible
- Fixed — a session that was disconnected and then logged off by the system now shows "Logged off (was disconnected)" instead of still reading "Disconnected"; the session detail adds a timeline (connected → disconnected → logged off) with an evidence-based reason from the pool's disconnect-logoff policy
- Changed — the Session Log is now an event list: each session shows its Logged on / Disconnected / Logged off events on their own line with the time, filterable by event type
- Fixed — the user activity popup mixed two periods (tiles over all time, list over 24h); it now uses one consistent 30-day window everywhere
- Changed — the Slowest logons card shows the selected period in its title (last 24 hours / 7 days / 30 days) so it is clear which window you are looking at
- Changed — the Active Directory 2FA texts were corrected: 2FA for AD accounts is handled by the monitor itself (set up at sign-in), not by the domain; an AD user now sees their real 2FA state
- Added — vCenter host card: CPU Ready per host (the actual vCPU wait) next to the vCPU overcommit ratio, with a combined label (Normal / Acceptable / Some CPU contention / Overcommit hurting); no extra vCenter calls
- Added — other monitoring tools (PRTG, Grafana, Zabbix) can read the key figures from /api/prtg; worded more clearly

## 1.5.1 — 2026-10-15

- Fixed — Large environments: Horizon calls time out less (8 → 30 s), big lists are fetched in pages and cached; Pools page no longer crashes with 'machines.filter is not a function'
- Fixed — When Horizon does not answer, pages show empty with a red banner instead of demo data
- Fixed — Disconnected time is measured from the moment the session disconnected; a ~ marks an estimate for sessions already disconnected at startup
- Fixed — User Activity 'Last seen' shows the last activity, not the session start
- Fixed — Session Log recorded every ended session as 'Log off'; 'Disconnect' is recorded again
- Fixed — Pools tab stayed on 'Loading…' with many pools (entitlement lookups)
- Fixed — E-mail: STARTTLS added as a third security mode; clearer error when a relay offers no AUTH
- Fixed — Agent Versions card stayed empty when two versions had the same count
- Fixed — Charts no longer draw one y-axis tick per session in large environments
- Fixed — A missing chart.js no longer breaks whole tabs; early localhost request (CORS error) removed
- Fixed — Less disk I/O: history downsampling runs with the 30-minute write; session log is written every 5 minutes
- Added — Clear buttons for SMTP user/password, Kemp API key and Teams webhook
- Added — System Health shows how long the last poll cycle took

## 1.5.0 — 2026-10-01

- 🔴 CRITICAL FIX — Session detail popup could open another user's session when a list was filtered or sorted; lookup is now by session id
- SECURITY FIX — /api/config/load exposed the Kemp API key, Teams webhook and local user hashes to every logged-in user; secrets are no longer sent to the browser
- Fixed — Alerts, history and session log only ran while a browser had the dashboard open; they now run on the server every 60 s
- Fixed — Logon time was never measured ('Avg login speed' showed 0s); it is now fetched per new session via the Help Desk API
- Fixed — The daily history archive was never written; a nightly rollup now keeps per-day totals, per-pool and per-host figures for 10 years
- Fixed — Alert e-mails had a broken logo; Dashboard showed 'Connection Servers 0/3'; login page and dashboard could be served from browser cache; listen port was fixed at 8383
- Added — History: 'Logon time per pool' (with golden-image markers and phase breakdown), 'Pool sizing & license', availability report under Uptime
- Added — Machine and session actions matching Horizon Console: Recover, Maintenance mode (also bulk), Remove (admin), Send message, 'Message all'
- Added — New alert type for red vCenter alarms; machines in Deleting/Maintenance are shown as 'transitional' and never alert
- Added — Daily log file (logs\vwm-YYYY-MM-DD.log), Support export with masking (and a strict mode), login diagnostics on the login page, logins survive a restart, manual at /manual
- Added — Listen port configurable (Settings, config.json or --port); Sessions/Machines/Alerts lists capped with 'Show all'
- Changed — Console, log file, API docs and e-mail reports fully in English

## 1.4.0 — 2026-09-25

- My Dashboard rebuilt on a free-form grid (GridStack): drag widgets by their title bar to reorder, resize them from any edge or corner, layout is saved per user and restored on reload. 'Reset layout' button restores default placement
- Widgets in My Dashboard are now identical to their originals on the tabs — one shared render per widget (Sessions Today vs Yesterday, Top Users, Pool Usage, Client Type, Session Duration, Top 5, Long Idle, Disconnected, Outdated Clients, Concurrent Logins, Night Sessions) or a live mirror of the tab's chart (all Charts, History, vCenter and Infrastructure charts). No more diverging copies
- Opening My Dashboard warms up all source tabs in the background so mirror-widgets load without visiting the tab first
- Widget '+' button on every card toggles between add and remove (green check when the widget is already on My Dashboard)
- New KPI row on Dashboard: Active sessions (connected/disconnected split + today's peak time), Idle > 30 min (count + longest), Available machines (with progress bar), Infrastructure (CS/UAG/ESXi status dots + certificate days). All four are clickable
- External vs Internal night sessions: route detection via UAG (external) or broker (internal), configurable night window in Settings > Alerts (default 22:00–06:00), counts in the card header, and a detail popup with All/External/Internal filter
- User Activity detail popup: active session shown separately with a live badge and click-through to the full session detail; previous sessions are clickable for a summary
- Sessions Today vs Yesterday: four coloured summary tiles (Connected, Disconnected, Peak today, Peak yesterday with time), gradient area chart with hover points and 'At HH:MM' tooltips
- Charts modernised throughout: gradient fills, hover points, dark tooltips with date/time, values printed above bars, larger chart heights, unified grid colour, 11px tick labels
- Session Trend tooltip now shows 'DD/MM at HH:MM'; Session Heatmap uses the new blue palette
- Sidebar: brighter labels, sidebar colour runs to the bottom of the page regardless of content length
- Machines table: CPU %, vGPU and IPv4 cells now use the regular font instead of monospace
- Dark mode: pastel tiles, status badges, GridStack placeholder and the generic modal now have proper dark variants
- Security: CORS is no longer wildcard — only the dashboard's own origin is allowed (extra origins configurable via 'cors_allowed_origins' in config.json); 95 additional output locations now HTML-escape Horizon/vCenter-supplied names and API error messages
- Fixed: Active Directory login never worked — the Base DN was read from a config key that was never written, and the LDAP bind used DOMAIN\user with the DNS domain name (rtvnoord.nl\user), which AD rejects. Login now binds as user@domain when a DNS name is configured, accepts 'user', 'DOMAIN\user' or 'user@domain' at the login screen, and recognises nested group membership. Every failed AD login now prints a diagnostic line on the console
- Fixed: vCenter alarms always returned an error — the REST endpoint /vcenter/alarm does not exist; alarms are now read via the SOAP API (pyvmomi) and filtered to the configured hosts
- Fixed: Pools tab filter (All / Issues only / Disabled) did nothing because its dropdown shared an element id with the Sessions pool filter
- Fixed: dashboard render crash ('totalAct is not defined') that blanked Session Overview, At a Glance, ESXi and Live Activity
- Fixed: Chart.js grid lines disappearing when defaults were replaced instead of mutated; 'Connected' sparkline on Sessions showed totals; 'Pool Occupancy per Hour' summary crashed on an undefined variable
- Fixed: removing a widget from My Dashboard could wipe other widgets' content; refreshing the page lost the My Dashboard layout
- Removed: 'Login speed' and 'Environment health' KPI cards (replaced by the new KPI row)

## 1.3.0 — 2026-09-21

- New Pools tab with health scores, capacity, sessions, golden image/snapshot, provisioning status, entitlements, naming config, and issue summaries per pool. Searchable and filterable
- Pool detail popup — click any pool for active sessions, machine list, and full configuration
- Compact pool summary on Dashboard — health pills + problem-pool table
- Live Activity Feed — real-time stream of logins, logoffs, disconnects, and machine errors
- Week comparison on Dashboard — this week vs last week for sessions, login speed, errors, and disconnects
- Login Speed Trending on Charts tab — 7-day bar chart with anomaly detection
- CSV export on Sessions and Machines tables
- Print button in topbar for a clean printable view of any tab
- Machine error and provisioning error alerting with configurable thresholds in Settings
- Bulk PROVISIONING_ERROR detection — warns of possible vCenter/storage issues when 3+ machines fail simultaneously
- Quick actions in At a Glance — clickable machine names open detail popups, 'Restart all' for error machines
- Certificate expiry warning in At a Glance when <30 days remaining
- Week overview in daily email report — peak sessions bar chart + summary
- Layered history downsampling — reduces history file by ~85% while keeping 30 days of data
- Dashboard layout: At a Glance + Live Activity (top), Machine Status (middle), Pools + ESXi + Sessions (bottom)
- Improved name resolution — AD lookup for alerts and activity feed, machine names instead of UUIDs
- VLAN fix — shows portgroup names instead of internal keys
- Visual upgrade — ring charts for Session Overview (donut), ESXi hosts (CPU/sessions per host), and Infrastructure (CS/GW status rings)
- Redesigned topbar — grouped layout with status badge, icon-only tool buttons, and clean separators

## 1.2.0 — 2026-09-17

- Two-factor authentication (2FA) for local accounts via any authenticator app
- VDI host filter — scope the entire dashboard to specific hosts or clusters
- Maintenance mode — suppresses alerts with a visible banner
- Per-alert snooze with flexible durations (15 min to 8 hours)
- System Health panel in Settings — monitor background loop status
- Session quality monitor — on-demand Blast/PCoIP metrics per session
- Bulk machine actions — select multiple machines, restart/reset/rebuild at once
- User Activity page with active days tracking and session history per user
- Combined session popup with quality metrics and action buttons
- Machine detail popup with CPU/Memory/vGPU tiles and recent sessions
- Dark mode (moon icon or press D) and keyboard shortcuts (1-9, R, /, Esc)
- Visual overhaul — colored tiles, status indicators, toast notifications, row highlighting
- Fixed email logo (CID embedding), session duration calculation, and disconnected tile names
- Atomic JSON writes and staggered background thread startup

## 1.1.0 — 2026-09-17

- Added live CPU and memory usage, vGPU profile, datastore, VLAN, and IP address to the Machines table and detail view
- Added VMware Tools status, uptime, disk/network detail, and hardware version to the machine detail view
- New "Top Busiest VMs" widget, showing which machines are waiting longest for CPU time (available via the My Dashboard widget picker)
- Fixed: the Machines table's "Details" button opened an older, separate screen that was missing the new machine info - it now opens the same detail view as clicking the row
- Fixed: Memory % always showed the same number on vGPU machines, no matter the real usage. Turns out this is a vSphere limitation, not something we can calculate around — GPU-enabled machines simply can't be measured the normal way. The dashboard now greys this out with a short explanation instead of showing a misleading number.

## 1.0.0 — 2026-09-17

- Renamed from "Horizon Monitor" to "Virtual Workspace Monitor" (the old name referenced a trademarked product name)
- New icon throughout the app and login screen
- Switched to semantic versioning (X.Y.Z) - previously a simple sequential number
- New in-app changelog, viewable under Settings
