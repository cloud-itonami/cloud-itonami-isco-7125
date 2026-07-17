# Security Policy

This project handles glaziers operating workflows. Treat vulnerabilities
as potentially high impact even when the demo data is synthetic — this
domain's failure modes include physical worker-safety risk and
building-envelope integrity risk.

## Do Not Disclose Publicly

Report privately before opening public issues for:

- credential exposure
- real glazier, site or operator data exposure
- authorization bypass
- Glazier Governor bypass
- audit-ledger tampering
- over-disclosure in reports or exports
- unsafe robot action dispatch
- any path that lets a proposal reach a glazing-installation-execution
  decision, or a site-safety-officer-override decision

## Reporting

Use GitHub private vulnerability reporting when available for the repository.
If that is unavailable, contact the repository maintainers through the
cloud-itonami organization before publishing details.

Include:

- affected commit or version
- reproduction steps
- expected and actual behavior
- impact on glazier/site data, policy enforcement or audit logging
- suggested fix, if known

## Production Guidance

- Store secrets outside Git.
- Keep real glazier/site/operator data outside this repository.
- Run policy tests before deployment.
- Export and review audit logs regularly.
- Use least privilege for operators and service accounts.
