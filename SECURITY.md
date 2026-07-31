# Security

A framework for forward-deployed engineering teams.

## Reporting a vulnerability

Report security issues privately to **aimperiale@vurtuolabs.com**. Do not open a public
issue for anything you believe is exploitable.

Include the version or commit you tested, the org edition and configuration if relevant,
what you observed, and the smallest set of steps that reproduces it. A proof of concept is
welcome but not required.

What to expect:

| Stage | Target |
|---|---|
| Acknowledgement that the report was received | 3 working days |
| Initial assessment, including whether it is accepted | 10 working days |
| Fix or documented mitigation for an accepted issue | 30 days, sooner where severity warrants |

We will tell you which way the assessment went either way. If a report is not accepted you
will get the reasoning, not silence. Credit is offered on any accepted report unless you ask
us not to.

## Supported versions

Security fixes land on the default branch. There are no long-lived release branches and no
backports to older commits, so the supported version is the current `main`.

## What this repository is, and what that means for risk

This repository contains process documentation, templates and configuration. It is not a
deployable package and contains no Apex, no LWC and no server-side code. Its risk
profile is that of content a person or an automated agent may act on, rather than of
running software.

## Threat model

- Templates and instructions here are read by humans and by coding agents. They are advisory, not authoritative.
- A change to an instruction file can change what an automated agent does downstream. Review diffs to instruction content with the same care as code.
- Nothing here executes on its own.

## Secrets

No API keys, tokens, passwords, certificates or org credentials are committed here.
Examples that need a credential use an obvious placeholder. If you find a real one,
treat it as a live incident and report it to the address above rather than opening an
issue.

## What this repository deliberately does not do

- It does not contain executable application code.
- It does not access any org, external service or user data.
- It does not collect telemetry.

## Using this content safely

- Review content before acting on it.
- Pin to a commit for automated use.
- Do not commit credentials, customer names or client data into templates.

## License and warranty

Released under the MIT License. It is provided without warranty of any kind, including
any warranty of security or fitness for a particular purpose. See [LICENSE](LICENSE).

Copyright (c) 2026 VurtuoLabs
