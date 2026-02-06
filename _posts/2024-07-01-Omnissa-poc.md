---
layout: post
title: Omnissa POC templates
subtitle: Repository of templates to assist with planning for a Omnissa platform Proof of Concept (POC)
tags: [Workspace ONE, Horizon, POC]
readtime: true
comments: true
author: Mathieu Beaugrand
---
Over the years, I’ve helped customers deploy a wide range of EUC solutions, whether as paid projects, trials, or full Proof of Concept (POC). One of the most common challenges I see is getting started with the prerequisites. Vendor documentation is often extensive, scattered, and sometimes confusing, which can make the initial setup harder than it needs to be.

To streamline this process, I began developing my own templates and tools to help customers capture the required information, track their progress, and accelerate deployment. Over time these resources have proven valuable across many engagements, so I decided it was finally time to share them with the wider community.

## Success criteria
Success criteria are often overlooked or left until later in a project, yet they are critical for defining what a successful trial or POC looks like. Many customers find it difficult to start from a blank page, so I created a template to make this easier and guide the discussion from day one.

[Omnissa-success-criteria.xlsx](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Omnissa-success-criteria.xlsx)
![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Omnissa-success-criteria.png)

## Logical diagrams
To help clients better understand how the Omnissa platform components interact, I created a set of simple, easy-to-consume logical diagrams. These diagrams provide a clear overview of the architecture and help support planning and stakeholder discussions.

| UEM | Horizon | UEM + Horizon 8 |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM.drawio.png)](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM.drawio.png) | [![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/Horizon.drawio.png)](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/Horizon.drawio.png) | [![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM+Horizon8.drawio.png)](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM+Horizon8.drawio.png) |
| [UEM.drawio](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM.drawio) | [Horizon.drawio](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/Horizon.drawio) | [UEM+Horizon8.drawio](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Logical-Diagrams/UEM+Horizon8.drawio) |

## Network diagrams
During a Proof of Concept, network diagrams help bridge the gap between design assumptions and real-world implementation. The following diagrams illustrate the high-level architecture, key integration points, and data flows involved in the Omnissa platform. They are intended to validate design decisions, highlight dependencies, and serve as a reference during testing and issue resolution.

| UEM |
|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Network-Diagrams/UEM-ACC-Connector-UAG-PowerShell.drawio.png)](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Network-Diagrams/UEM-ACC-Connector-UAG-PowerShell.drawio.png) |
| [UEM-ACC-Connector-UAG-PowerShell.drawio](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Network-Diagrams/UEM-ACC-Connector-UAG-PowerShell.drawio) |

## Prerequisites workbook
I’ve also created a comprehensive prerequisites spreadsheet that captures all technical requirements in one place. It allows customers to record key configuration details as they work through the checklist. Once complete, the same document can be used as an as-built record, reducing duplication and saving time.

[Omnissa-prerequisites.xlsx](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Omnissa-prerequisites.xlsx)
![](https://blog.beaugtech.com/assets/img/2024-07-01-Omnissa-poc/Omnissa-prerequisites.png)