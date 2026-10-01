# Changelog

All notable changes to Virtual Workspace Monitor. The same list is shown in the app under Settings > Changelog.

## 1.5.0 — 2026-10-01

- 🔴 CRITICAL FIX — session detail popup could show the wrong user: the Detail button in Disconnected Sessions, Long Idle Sessions, Top 5 Longest Connected and the filtered Sessions table passed a position in its own sorted/filtered list, but the popup looked that position up in the full session list. With filters or sorting active this opened another user's session, including its Disconnect / Log off / Restart buttons. The popup now looks sessions up by session id and machine id only
- 🟠 FIX — the poll cycle (history, alert engine, session log, activity feed) only ran while a browser had the dashboard open, so no alert e-mails were sent when nobody was logged in and history had gaps at night and in weekends. It now runs on the server every 60 seconds; multiple open tabs no longer trigger multiple cycles
- 🟠 FIX — logon time was never measured: the session list does not contain it, so 'Avg login speed' always showed 0s. Logon segments are now fetched once per new session via the Help Desk API (total, profile load, GPO, shell load) and cached
- 🟠 FIX — the daily history archive was only written when the main history overflowed, which the downsampling prevented; anything older than 30 days was silently lost. A nightly rollup now summarises every completed day (totals, per-pool peak/average sessions and machines on, % of day a pool was full, per-host memory/CPU peaks, logon time per pool, license peak, coverage) and the archive is backfilled on first start
- 🟠 FIX — alert e-mails from the alert engine had a broken logo; all mail now goes through the same sender as the reports
- 🟠 FIX — Dashboard health strip showed 'Connection Servers 0/3' (Horizon's health-metrics counts decommissioned entries and flags expiring certificates); it now shows the real online count with Horizon's state in a tooltip
- 🟠 FIX — the login page and dashboard could be served from browser cache after an update; both are sent with Cache-Control: no-store
- 🟠 FIX — the listen port was fixed at 8383 despite the manual; it is now configurable (Settings > Connections, listen_port in config.json, or --port on the command line)
- SECURITY FIX — /api/config/load returned most of config.json to every logged-in user, including viewers: the Kemp API key, the Teams webhook URL and the local user list with password hashes and encrypted TOTP secrets. Secrets are no longer sent to the browser at all (fields show 'configured' instead) and non-admins receive only the settings the dashboard needs
- History tab: new 'Logon time per pool' card — one line per pool over the selected period, markers on the days a pool's golden image or snapshot changed, phase breakdown per pool (profile / GPO / shell, p90) and the five slowest logons. Golden image changes are now logged
- History tab: new 'Pool sizing & license' card — per pool machines vs peak, p95 of daily peaks, days full and headroom; license concurrent-connection peak, p95, trend and headroom against the licensed count you enter under Settings > License
- History tab: 'Availability (Uptime)' gained an availability report from the daily archive — CS / UAG / vCenter %, time without a free machine per pool, monitor coverage, per month
- History tab: Peak Usage per Day shades days on which the monitor measured less than 80% of the day; GPU Trend and Machines Active per Pool follow the global period selector (own selectors removed); the pool-machines card shows daily peak with the average in the tooltip and explains when they are equal
- More machine and session actions, matching the Horizon Console: Recover, Enter / Exit maintenance mode (single and bulk), Remove (admin only, with 'delete from disk' and 'force log off' options and the machine name typed to confirm), Send message to a session (info / warning / error) — from the session popup, the machine popups, and 'Message all' for the filtered list on the Sessions tab. Horizon's own error text is shown when an action fails
- Session popup shows the logon timeline; Session Log has a sortable Logon column with the phase breakdown in the tooltip
- Alerts: new alert type for red (critical) vCenter alarms with e-mail (Settings > Thresholds & Alerts); At a Glance and the alert engine share one definition of machine error states — Deleting / Maintenance / Already used are shown as 'in a transitional state' without a Restart button and never alert
- Log file: everything printed to the console is also written to logs\vwm-YYYY-MM-DD.log (JSON Lines), rotated daily, kept 14 days, downloadable from Settings > System Health
- Support export (Settings > System Health, admin only): raw responses of ~27 Horizon REST endpoints in one zip for troubleshooting, with user/machine/server/gateway names, SIDs, IPs, DNS names, AD containers and license keys masked; a 'strict' option also masks pool, farm, application and entitlement names. Nothing is sent automatically
- Login page: 'Having trouble signing in?' diagnostics (cookie test, server reachability, version, clock difference, Copy button); a clear explanation instead of a redirect loop when the browser does not keep the session cookie
- Logins survive a restart of the monitor (sessions are stored hashed next to the exe)
- Startup: a clear message instead of a traceback when the port is already in use; the manual is served by the monitor itself at /manual (sidebar and login page), from the exe folder or a manual\ subfolder
- Large environments: Sessions table shows the first 100, Machines table the first 200 and Active Alerts the first 50, each with a 'Show all' link; filters still search the full set
- System Health shows the poll cycle as its own background loop; vCenter alarm lookups are cached for 90 seconds
- Remaining Dutch labels replaced by English

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
