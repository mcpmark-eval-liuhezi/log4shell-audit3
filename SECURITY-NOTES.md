# Security Notes: Log4Shell (CVE-2021-44228)

## Summary

Log4Shell is a critical remote-code-execution vulnerability in Apache Log4j 2, an open-source Java logging framework widely used in enterprise applications and cloud services. An attacker who can control log messages or log message parameters can execute arbitrary code loaded from LDAP (and other JNDI) servers when message lookup substitution is enabled.

## CVE Identifier

- **CVE-2021-44228** (commonly known as "Log4Shell")

## Affected Versions

- **Affected:** Apache Log4j 2 versions **2.0-beta9 through 2.14.1** (inclusive) — specifically the `log4j-core` component. Log4j 1.x is End of Life and unpatched for this issue.
- Only `log4j-core` is directly affected; `log4j-api` is not directly vulnerable but should be kept at the same version as `log4j-core` for compatibility.

## Fixed Version

- **First fix:** 2.15.0 (addressed CVE-2021-44228, but the fix was incomplete in some non-default configurations)
- **Complete fix:** 2.16.0 — disables and removes JNDI message lookups
- **Recommended:** 2.17.0 or newer for Java 8 and later
- **Backports:** 2.12.2 / 2.12.3 for Java 7; 2.3.1 for Java 6

## Severity

- **CVSS 3.1 base score: 10.0 (Critical)**
- Vector: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H`
- Attack vector: Network; Attack complexity: Low; Privileges required: None; User interaction: None; Scope: Changed; Confidentiality/HIntegrity/Availability impact: High

## Remediation Summary

1. **Upgrade** `log4j-core` to 2.16.0+ (2.17.0+ for Java 8+); use backports 2.12.2/2.12.3 (Java 7) or 2.3.1 (Java 6) as needed.
2. **Detect vulnerable deployments** via dependency trees (`mvn dependency:tree`, `gradle dependencies`), grepping for `JndiLookup.class` in deployed jars, and checking logs for `[LD_Logging]` markers.
3. **Hardening:** set `-Dlog4j2.formatMsgNoLookups=true` for 2.10–2.14 (note: does not fully mitigate CVE-2021-44228 itself); remove `org/apache/logging/log4j/core/lookup/JndiLookup.class` from the jar as a supplementary measure; avoid Thread Context Map (MDC) entries from untrusted sources in PatternLayouts.
4. **Automated dependency scanning:** enable GitHub Dependabot alerts/security updates; use OWASP Dependency-Check, Snyk, Trivy, or Grype in CI; add build constraints to fail on vulnerable versions (e.g., Gradle `strictly("[2.17, 3[")`).

## Sources

- NVD: https://nvd.nist.gov/vuln/detail/CVE-2021-44228
- Apache Logging Services Security page: https://logging.apache.org/log4j/2.x/security.html
- CISA Alert (Dec 10, 2021): https://www.cisa.gov/news-events/alerts/2021/12/10/apache-releases-log4j-version-2150-address-critical-rce-vulnerability-under-exploitation
- OSV/GHSA advisory: https://github.com/advisories/GHSA-jfh8-c2jp-5v3q
- Gradle blog on the Log4j vulnerability: https://blog.gradle.org/log4j-vulnerability
- Fossa Log4j cheatsheet: https://go1.fossa.com/rs/246-JVA-804/images/Log4j%20Cheatsheet.pdf
