# CI/CD Connector Requirements for Rapid Clients

_Updated June 5, 2026_

## Summary

Rapid's connector platform uses a CI/CD (Continuous Integration/Continuous Deployment) pipeline to deliver bug fixes, compliance changes, and new features automatically. To receive these updates at no additional cost, clients must allow the deployment access described in this document.

If automated deployment is blocked by client security restrictions, Rapid staff will need to perform the work manually — and that time will be billed at current T&M rates. Our goal is to work with your IT team upfront so that all future updates can be delivered automatically, securely, and without extra cost.

*New to Rapid CI/CD connectors? Start with the [CI/CD Connector Benefits](https://github.com/Rapid-POS/Miscellaneous-Documents/blob/main/CICD-Connector-Benefits.md) overview.*

## Table of Contents

- [Update and Notification Policy](#update-and-notification-policy)
- [IT Checklist](#it-checklist)
- [Technical Requirements](#technical-requirements)
- [Installation Requirements](#installation-requirements)
- [Upgrade Requirements](#upgrade-requirements)
- [Offline Station Requirements](#offline-station-requirements)
- [Replication Limitations](#replication-limitations)
- [Common Causes of Failed Deployments](#common-causes-of-failed-deployments)
- [Questions](#questions)

---

## Update and Notification Policy

Your monthly connector subscription includes periodic updates as they become available.

**Scheduled upgrades:** Rapid will attempt to provide at least 24 hours' notice prior to scheduled connector upgrades, along with an estimated deployment window. In rare circumstances, an important update may need to be deployed on short notice or without notice.

**Manual deployment fees:** If an automated deployment is blocked due to client security restrictions — requiring Rapid staff to install manually, troubleshoot, or coordinate with client IT — that work will be billed at current T&M rates.

#### [↑ Back to Top](#table-of-contents)

---

## IT Checklist

Use this checklist before any installation or upgrade to confirm your environment is ready.

- [ ] Administrator access has been provided to Rapid
- [ ] Outbound communication to `https://dev.azure.com` (Azure DevOps) is permitted through your firewall, proxy, and web filter
- [ ] PowerShell script execution is enabled
- [ ] Endpoint protection, anti-malware, and application control software will not block Rapid deployment scripts or files
- [ ] All Counterpoint users will be logged out during the deployment window
- [ ] Offline stations (if applicable) will be powered on and reachable

#### [↑ Back to Top](#table-of-contents)

---

## Technical Requirements

Rapid's Azure-based deployment platform requires permission to:

- Connect to your server to deploy connector updates
- Write, replace, and update files within Rapid connector installation directories
- Stop and restart Rapid connector-related services as needed during an upgrade
- Run without being blocked, quarantined, or rolled back by endpoint protection, anti-malware, or application control software
- Complete deployments without manual approval or intervention for each release

#### Minimum System Requirements

| Component | Minimum Version |
|---|---|
| Counterpoint | 8.5.6.2 |
| SQL Server | 2016 |
| Operating System | Windows Server 2016 or Windows 11 Pro |
| PowerShell | 5.1 |

#### Network Requirements

Outbound communication to **Microsoft Azure DevOps** — including `https://dev.azure.com` — must be permitted through firewalls, web filters, proxy servers, and endpoint security products.

- Review this guide from Microsoft to learn more: [https://learn.microsoft.com/en-us/azure/devops/organizations/security/allow-list-ip-url?view=azure-devops&tabs=IP-V4](https://learn.microsoft.com/en-us/azure/devops/organizations/security/allow-list-ip-url?view=azure-devops&tabs=IP-V4) 

#### PowerShell Requirements

- PowerShell script execution must be enabled for Rapid deployment and upgrade processes.
- Endpoint protection and application control software must not block approved Rapid deployment scripts.

#### [↑ Back to Top](#table-of-contents)

---

## Installation Requirements

1. Connectors are installed on the Counterpoint server.

   **Exception:** The *Rapid Custom Task Agent* is generally installed on the Counterpoint server, but can be installed on the SQL Server when SQL Server is not co-located with Counterpoint.

2. The server environment must meet the minimum requirements for the specific connector being installed. See [Minimum System Requirements](#minimum-system-requirements) for details.

3. Administrator-level server access must be provided for the initial installation. Most clients run Counterpoint and SQL Server on the same machine. If your Counterpoint and SQL Server are on separate servers, Rapid will need administrator access to both.

4. Administrator-level connector platform access and credentials must be provided.

5. Initial installation will be scheduled with the client based on mutual availability.
   - All users must be logged out of Counterpoint during installations unless Rapid specifies otherwise.
   - Installations are typically scheduled after the close of business.

   **Exception:** The *Rapid CP API* and *Rapid Custom Task Agent* can typically be installed during business hours.

#### [↑ Back to Top](#table-of-contents)

---

## Upgrade Requirements

1. Connector upgrades are deployed automatically through Rapid's deployment platform.

2. All users must be logged out of Counterpoint during upgrades unless Rapid specifies otherwise.

3. Upgrades that include database schema changes are typically scheduled after the close of business.

4. See [Update and Notification Policy](#update-and-notification-policy) for advance notice details.

#### [↑ Back to Top](#table-of-contents)

---

## Offline Station Requirements

*This section applies only to clients using offline stations.*

1. Both the initial installation and all upgrades require the server database to be provisioned and rebuilt on all offline stations.

2. All offline stations must be powered on during installations and upgrades.

3. If an offline station is not powered on, the database rebuild will be queued and will run the next time that station is powered on.

4. Users must not use Counterpoint while a database rebuild is in progress.

#### [↑ Back to Top](#table-of-contents)

---

## Replication Limitations

*This section applies only to clients using multi-site replication.*

Certain CI/CD connectors are not currently supported in multi-site replication environments. If you have a hub server and one or more remote servers, consult with Rapid before requesting a CI/CD connector.

#### [↑ Back to Top](#table-of-contents)

---

## Common Causes of Failed Deployments

If an automated deployment fails, it is most commonly due to one of the following:

- Azure DevOps traffic blocked by a firewall, proxy, or web filter
- Deployment files quarantined by endpoint protection or anti-malware software
- PowerShell execution disabled or restricted
- Connector services unable to be stopped or restarted
- Insufficient administrative permissions
- Users logged into Counterpoint during the deployment window
- Offline stations powered off or unreachable during deployment
- Application control software blocking Rapid deployment processes

#### [↑ Back to Top](#table-of-contents)

---

## Questions

Contact the **Rapid Programming Team** at [support@rapidpos.com](mailto:support@rapidpos.com) if your IT staff need additional technical information about the deployment process or access requirements.

#### [↑ Back to Top](#table-of-contents)
