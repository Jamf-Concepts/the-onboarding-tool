# The Onboarding Tool

> **A faster, friendlier way to build Jamf Mac onboarding configurations. No XML required.**

![macOS 15+](https://img.shields.io/badge/macOS-15%2B-success)
![Swift](https://img.shields.io/badge/Swift-SwiftUI-orange)

[![The Onboarding Tool home screen, with a card for Setup Manager and a card for Setup Checklist.](Screenshots/home-screen.png)](Screenshots/home-screen.png)

---

## Table of contents

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [First launch](#first-launch)
5. [Connecting Jamf Pro](#connecting-jamf-pro)
   - [Creating an API role and client](#creating-an-api-role-and-client)
   - [Required permissions](#required-permissions)
   - [External services](#external-services)
6. [What you can build](#what-you-can-build)
7. [Previewing before you deploy](#previewing-before-you-deploy)
8. [Exporting and deploying](#exporting-and-deploying)
9. [Documentation](#documentation)
10. [Credential storage and security](#credential-storage-and-security)
11. [Privacy and analytics](#privacy-and-analytics)
12. [Troubleshooting](#troubleshooting)
13. [Dependencies and Software Bill of Materials](#dependencies-and-software-bill-of-materials)
14. [Terms of Use](#terms-of-use)
15. [Help and Feedback](#help-and-feedback)

---

## Overview

The Onboarding Tool (TOT) is a native macOS app that gives you one graphical editor for Jamf's two primary Mac onboarding solutions: **Jamf Setup Manager**, the built-in enrollment workflow engine, and **Jamf Setup Checklist**, the Jamf Concepts first-login checklist and its Welcome screen.

Both read their configuration from a property list. Historically you produced that plist by hand-editing XML, or by typing key names into a Custom Settings payload. TOT replaces that with a guided form: every documented key in both profiles is available, every row carries plain-language help, and a live preview shows you the generated XML as you type.

TOT does not change how Setup Manager or Setup Checklist behave. It produces the same configuration files you would build by hand.

### Key capabilities

| Capability | Details |
| --- | --- |
| Two modules, one app | Setup Manager (`com.jamf.setupmanager`) and Setup Checklist (`com.jamf.setupchecklist` plus `com.jamf.setup.welcome`) |
| Complete key coverage | Every documented key in each profile, not just the common ones, with help text on every field |
| Live Plist Preview | Syntax-highlighted XML that syncs both ways: edit the form or edit the XML |
| Workflow Preview | See the enrollment window, Welcome screen, and checklist without deploying to a test Mac. Pick a language, and toggle whether the *enrolling Mac* is in light or dark appearance |
| Validation warnings | Flags empty labels, duplicate step identifiers, missing triggers, and other common mistakes before you export |
| Jamf Pro and Jamf School | A mode toggle tailors the editor and strips settings Jamf School cannot use |
| Browse Icons | One picker across the App Installers catalog, your uploaded Jamf Pro icons, your Self Service branding, SF Symbols, a local file, and a Create Icon tab that builds one from any image |
| Light and dark appearance | Follows your Mac by default, or pin either one in Settings under General |
| Jamf Pro browsers | Insert real policy triggers, departments, and buildings instead of copying IDs by hand |
| Saved projects | Name a configuration and it autosaves as you work |
| Localization | Localize any supported field, then translate every language at once using macOS's on-device Translation framework |
| Substitution variables | A click-to-copy glossary of every supported token, grouped by where it is valid |
| Shell workflow presets | Ready-made scripts for routine enrollment tasks, including a built-in IANA time zone picker |
| Import anything | Load an existing plist, or paste XML straight into the preview |

### What it does not do

- It does not manage devices, scope profiles, or send commands to any Mac.
- It does not modify your Jamf Pro instance, apart from uploading an icon when you ask it to.
- It does not require a Jamf Pro connection at all. Editing and export features work offline.


**See close to what the user will see, before you deploy**

The Workflow Preview renders your configuration close to how the end user will experience it. Every element traces back to a field in the editor. Ideal for confirming branding, Icons, input fields and more before deploying out for further debugging. 

![The Setup Manager editor beside the Workflow Preview, showing the same title, message, and three actions in both windows.](Screenshots/workflow-preview-side-by-side.png)

**Fill out a form instead of hand-editing XML**

![The Setup Checklist editor, with global settings and an ordered list of steps in the sidebar and a guided form on the right.](Screenshots/checklist-editor-layout.png)

**Find an icon without hunting for a URL**

Browse the app catalog, your Jamf Pro icons, SF Symbols, or a local file, all from one explorer.

![The Browse Icons explorer showing the Default Icons catalog.](Screenshots/browse-icons-explorer.png)

---

## Requirements

- **macOS 15.0 (Sequoia) or later.** Native Swift and SwiftUI.
- One third-party dependency: the [TelemetryDeck Swift SDK](https://github.com/TelemetryDeck/SwiftSDK) (MIT), used only for the anonymous launch notification described in [Privacy and analytics](#privacy-and-analytics).
- A **Jamf Pro connection is optional.** It powers the Browse Icons explorer and the policy trigger, department, and building browsers.
- Your configuration stays on your Mac. No cloud storage, no sync.

---

## Installation

1. Download the latest `.pkg` from [Releases](../../releases).
2. Double-click it and follow the installer prompts. The package installs The Onboarding Tool to `/Applications`.
3. Launch **The Onboarding Tool**.

The app is signed by Jamf Software, LLC. and notarized by Apple.

---

## First launch

The app opens to the Home screen. Nothing needs configuring before you start. Pick a module and begin. 

Two things worth knowing on day one:

- **Every launch starts fresh** with an empty, untitled configuration. Nothing is auto-restored. Use **Open Project** to pick up earlier work, and note that autosave only begins once you have named a project with **Save as Project**. 

---

## Connecting Jamf Pro

The connection is optional and read-only apart from icon upload. Open **Settings** and go to the **Jamf Pro** tab. Three ways in: the gear in the toolbar, `Cmd-,`, or **Settings** in the app menu. While no connection is saved, Settings opens on that tab for you.

| Field | Value |
| --- | --- |
| Jamf Pro URL | Your instance URL, for example `https://yourorg.jamfcloud.com`. HTTPS is required and trailing slashes are stripped for you |
| Authentication Method | **API Client** (OAuth) or **Username and Password** (Basic Auth). API Client is recommended |
| Client ID / Client Secret | Shown when API Client is selected |
| Username / Password | Shown when Basic Auth is selected |

Click **Test Connection** to verify, then **Save**. Nothing is written to the Keychain until you save, so a partly-typed secret is never stored. Once saved, the connected hostname appears under the window title, which is how you tell a sandbox instance from production at a glance.

### Creating an API role and client

1. In Jamf Pro, go to **Settings → System → API Roles and Clients**.
2. On the **API Roles** tab, click **New** and grant the privileges in the table below.
3. On the **API Clients** tab, create a client, assign that role, and generate a client secret.
4. Copy the Client ID and secret into **Settings → Jamf Pro**.

### Required permissions

As a best security practice, don't grant permissions to an API client unless you need them. 

| Feature | Required privilege |
| --- | --- |
| Connection test | None beyond valid credentials |
| Browse Icons, uploaded icons | None beyond valid credentials |
| Create Icon, upload to Jamf Pro | None beyond valid credentials |
| Policy Trigger browser | `Read - Policies` |
| Department options in User Entry | `Read Departments` |
| Building options in User Entry | `Read Buildings` |
| App Installer catalog refresh | `Read Mac Applications` |
| Self Service Branding source | `Read Self Service Branding Configurations` |

The Self Service Branding source works in two steps. Reading the branding configuration needs a privilege, but the image URL it returns serves without authentication, which is what makes it valid in an exported profile: the enrolling Mac holds no credentials. That URL carries your instance hostname, so a profile built from it is tied to that Jamf Pro server.


### External services

TOT reaches three static endpoints, none of which require authentication or send any data about you.

| Host | Purpose |
| --- | --- |
| `raw.githubusercontent.com` (jamf/Setup-Manager) | Promo icon for the Setup Manager tile on the Home screen |
| `raw.githubusercontent.com` (Jamf-Concepts/setup-checklist) | Promo icon for the Setup Checklist tile |
| `raw.githubusercontent.com` (Installomator/Installomator) | The live `Labels.txt` list for the Installomator label picker, with an offline cache fallback |

---

## What you can build

**Setup Manager** configures the full enrollment workflow: branding and display with light and dark variants, the ordered list of actions, computer identity and naming with substitution variables, an optional user-entry form, webhooks, network connectivity checks, a help button with a QR code, and post-workflow behavior.

Seven action types are supported: Installomator, Shell Command, Run Policy, Watch Path, Wait for User Entry, Recon, and Wait.

**Setup Checklist** configures the checklist users work through at first login, plus the Welcome screen shown before it. Seven step types are supported: Message, Open, Script, Default App, Screen Sharing, Dock, and Wallpaper.

Step-by-step walkthroughs for both live in the [wiki](../../wiki).

---

## Previewing before you deploy

Two previews, and they answer different questions.

**Plist Preview** shows the XML your form produces, syntax highlighted, updating as you type. It is editable: paste XML in and click **Apply** to pull it back into the form. Advisory validation warnings appear above it for problems that are easy to make and annoying to debug on a device, and they never block export.

**Workflow Preview** renders a close approximation of what the end user sees, so you can check branding and wording without deploying to a test Mac. Pick any language you have localized and toggle a light or dark simulation.

Assets stored at local paths on managed devices show as gray placeholders, because those files do not exist on your admin Mac.

---

## Exporting and deploying

**Export as `.mobileconfig`** produces a fully-formed Configuration Profile with the preference domain embedded. Upload it in Jamf Pro under **Computers → Configuration Profiles → New → Upload**, then assign scope. Profiles export unsigned and unscoped.

**Export as `.plist`** produces the raw plist, named for the correct preference domain, for use as a Custom Settings payload or deployment by script.

Setup Checklist produces **two** profiles, one for the checklist and one for the Welcome screen. Deploy both, or the Welcome screen will not appear.

---

## Documentation

The wiki is the full admin guide.

| Page | Contents |
| --- | --- |
| [Features and Navigation](../../wiki/Features-and-Navigation) | Getting around, connecting Jamf Pro, both previews, saved projects, localization, import and export |
| [Setup Manager Module](../../wiki/Setup-Manager-Module) | Build an enrollment workflow start to finish, every action type, substitution variables, Jamf School differences |
| [Setup Checklist Module](../../wiki/Setup-Checklist-Module) | Build a checklist and Welcome screen, every step type, deployment |
| [Troubleshooting](../../wiki/Troubleshooting) | Symptoms, causes, and fixes, plus what to include in a bug report |

---

## Data

| Item | Where it is stored |
| --- | --- |
| Jamf Pro password and API client secret | macOS **Keychain** only, never in preferences |
| Server URL, username, client ID | App preferences |
| Saved projects | `~/Library/Application Support/TheOnboardingTool/Projects`, configuration only, never credentials |

To remove stored credentials, open **Settings → Jamf Pro** and click **Clear Credentials**. This deletes the password or client secret from the Keychain and removes the username and client ID from preferences. The server URL is kept on purpose, so you can reconnect to the same instance without retyping it.

TOT runs no local processes. Shell commands and scripts you configure are data: they are written into a plist and executed later by Setup Manager or Setup Checklist on the enrolling Mac.

---

## Privacy

We use TelemetryDeck to know how often the app is opened. That helps us decide if we should keep working on the idea. The information is anonymous and you can disable it in Settings.

To turn it off, open **Settings** (the gear in the toolbar) and enable **Opt out of analytics**. 

See [Jamf's Privacy Policy](https://www.jamf.com/trust-center/privacy/privacy-policy/) for information on data handling.

---

## Troubleshooting

Symptoms, causes, and fixes live on the wiki: **[Troubleshooting](../../wiki/Troubleshooting)**. It covers missing fields, connection and privilege errors, icon and image sources, appearance, export and deployment, the Welcome screen, localization, and autosave.

Two things worth knowing without leaving this page:

- Blue **Browse** buttons query Jamf Pro and need a connection. Green ones work offline.
- **Test Connection** needs no privileges, so it passes on a role with nothing granted. If a browser fails after a successful test, the privilege is missing, not the connection.

**Logging.** Debug logging is off by default. Turn it on in **Settings → General**, reproduce the problem, then click **Export Log**. The log records API URLs, auth type, status codes, and timing, never credentials or tokens.

---

## Dependencies and Software Bill of Materials

The Onboarding Tool uses one third-party dependency, pulled in via Swift Package Manager:

| Package | Version | License | Home repository | License file |
| --- | --- | --- | --- | --- |
| TelemetryDeck SwiftSDK | 2.14.2 | MIT | [github](https://github.com/TelemetryDeck/SwiftSDK) | [LICENSE](https://github.com/TelemetryDeck/SwiftSDK/blob/main/LICENSE) |

See [Privacy](#privacy) for what that dependency reports and how to turn it off.

---

## Help and Feedback

We welcome your feedback submitted via [GitHub Issues](https://github.com/Jamf-Concepts/the-onboarding-tool/issues).

If you are reporting a bug, turn on debug logging first, reproduce the problem, then export the log and attach it. Include the version from **The Onboarding Tool → About The Onboarding Tool**.

---

## Related Projects

- [Jamf Setup Manager](https://github.com/jamf/Setup-Manager) and its [Configuration Profile reference](https://github.com/jamf/Setup-Manager/blob/main/ConfigurationProfile.md)
- [Jamf Setup Checklist](https://github.com/Jamf-Concepts/setup-checklist)
- [Installomator](https://github.com/Installomator/Installomator) label list

---

## Security

Information security is a team effort. If you discover a security vulnerability in our software, please report it through [Jamf's Vulnerability Disclosure Program](https://www.jamf.com/security/vulnerability-disclosure/).

For more information about Jamf's security program, please see:
[https://security.jamf.com/](https://security.jamf.com/)

---

## Terms of Use

The Onboarding Tool is made available under the [Jamf Concepts Use Agreement](https://concepts.jamf.com/agreement).

---

Copyright 2026, Jamf Software LLC.
