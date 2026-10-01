# Security

Virtual Workspace Monitor holds administrator credentials for Horizon, vCenter and Active Directory, and it can restart, reset, rebuild and remove virtual machines. That makes it worth being careful about, so here is how it handles security and how to report a problem.

## Reporting a vulnerability

If you find something, please don't open a public issue. Use **Report a vulnerability** under the Security tab of this repository (GitHub private vulnerability reporting). Only I can see those reports.

Tell me what you found, which version (shown in the dashboard header), and how to reproduce it. I'll confirm within a few days and tell you what I intend to do. This is a spare-time project, so a fix may take a little while; I'll keep you informed, and I'll credit you in the changelog if you want.

Please give me a reasonable amount of time to release a fix before you publish anything.

## Supported versions

Only the latest release gets security fixes. If you're on an older version, update first — the changelog lists what changed.

## What's in scope

- The monitor itself: the executable, the dashboard, the API it exposes on its listen port
- The way it stores configuration, credentials, sessions and history on disk
- The way it talks to Horizon, vCenter, AD, the load balancer and SMTP

Out of scope: vulnerabilities in Omnissa Horizon, VMware vSphere, Windows or the bundled third-party libraries themselves (report those to their vendors), and findings that require already having administrator access to the machine the monitor runs on.

## How it is built

- **Credentials** are encrypted with Windows DPAPI, bound to the Windows account that runs the monitor. `config.json` copied to another machine or account does not reveal them.
- **Login sessions** are stored as SHA-256 hashes; the file on disk cannot be turned back into a usable cookie. Cookies are `HttpOnly` and `SameSite=Lax`.
- **Brute force**: 5 failed logins per IP address within 5 minutes block that address for a while. Optional TOTP two-factor authentication.
- **Roles**: viewer, operator, admin. Machine actions need operator; removing a machine, configuration changes and the support export need admin. Checks happen on the server, not only in the browser.
- **Transport**: the dashboard is served over plain HTTP by default. Run it on a management network, or behind a reverse proxy that terminates TLS, if you expose it more widely. Built-in HTTPS is on the roadmap.
- **Nothing phones home.** No telemetry, no update check, no cloud. The only outbound connections are to the servers you configure.
- **Support export** masks user names, SIDs, machine, server and gateway names, IP addresses, DNS names, AD containers and license keys before writing the zip; a strict mode also masks pool and farm names. It only runs when an admin clicks the button and never leaves the machine on its own.
- **Audit**: logins, configuration changes and machine actions are written to the daily log file with the user who performed them.

## Things you should do yourself

- Give the Horizon and vCenter accounts the minimum privileges you need. Read-only is enough for monitoring; only grant the machine-action privileges if you want the Restart / Reset / Rebuild / Remove buttons to work.
- Don't expose port 8383 to the internet.
- Keep the folder the monitor runs from readable only by the account that runs it; the history and session-log files contain user names and machine names.
