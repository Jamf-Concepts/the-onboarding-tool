# Security Policy

The Onboarding Tool is a native macOS app that builds Jamf Setup Manager and Setup Checklist configuration profiles through a guided form editor, with an optional Jamf Pro connection (OAuth 2.0 Client Credentials, stored in the macOS Keychain) for browsing resources such as policy triggers, departments, buildings, and icons, and for uploading icons directly to Jamf Pro. Vulnerabilities in how the app handles credentials, parses imported plist or XML, or authorizes writes back to a Jamf Pro instance are taken seriously.

For Jamf Concepts' broader security posture, see **[concepts.jamf.com/en/security](https://concepts.jamf.com/en/security/)**.

## Supported versions

Security fixes are applied to the latest released version. Please make sure you can reproduce an issue on the most recent release before reporting it.

## Reporting a vulnerability

**Please do not open a public GitHub issue for security vulnerabilities.**

Instead, report privately using one of:

- Jamf's [Vulnerability Disclosure Program](https://www.jamf.com/trust-center/vulnerability-disclosure/), or
- The **Report a vulnerability** button under this repository's **Security** tab.

Please include:

- A description of the issue and its potential impact.
- Steps to reproduce, or a proof of concept.
- The Onboarding Tool version, macOS version, and (if applicable) Jamf Pro version you observed it on.

You can expect an initial acknowledgement within a few business days. Once a fix is available, the report will be disclosed publicly with credit to the reporter, unless you ask to remain anonymous.

## Scope

In-scope examples: mishandling or leakage of OAuth credentials (Keychain storage, logging, or disk writes), unsafe XML or plist parsing when importing existing configurations, any path that causes unintended API writes or icon uploads to a Jamf Pro instance without admin intent, or insecure use of the macOS on-device Translation framework for configuration data.

Out of scope: vulnerabilities in Jamf Pro, Jamf Setup Manager, Jamf Setup Checklist, macOS, or other third-party services. Please report those to the relevant vendor. The correctness of the generated plist configuration output is a user responsibility and is not considered a security issue in this context.
