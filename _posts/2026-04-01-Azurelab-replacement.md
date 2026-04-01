---
layout: post
title: Azure Lab Services retirement - Choosing the right EUC platform for what comes next
subtitle: Don’t just replace a service, elevate your EUC platform. Discover why Omnissa Horizon Cloud succeeds where AVD + Nerdio falls short.
tags: [Azure, Horizon, App, DEM]
readtime: true
author: Mathieu Beaugrand
---
The retirement of Azure Lab Services isn’t just another platform change. Azure Lab Services didn’t just provide infrastructure, it removed complexity. Its retirement doesn’t just remove a service. It puts that complexity back on you. It’s a forcing function to rethink how organisations deliver training environments, proof-of-concept platforms, and secure, repeatable desktop experiences.

For years, Azure Lab Services provided something rare in the cloud world: **Simplicity with control**.

Now, with its [retirement scheduled for June 2027](https://learn.microsoft.com/en-us/azure/lab-services/retirement-guide), that simplicity is gone, and organisations are being pushed toward new architectures and Microsoft acknowledges there is no like-for-like replacement.

Microsoft’s guidance is clear:
- Move to Azure Virtual Desktop (AVD)
- Complement it with tools like Nerdio for management

But here’s the real question: **Are you replacing a service... or redesigning your entire EUC platform?**

---

## From simplicity to assembled solutions

Azure Lab Services abstracted complexity:
- Provisioning was simple
- Lifecycle was controlled
- Environments were repeatable

In contrast, the recommended AVD + Nerdio approach is not a single solution. It’s a composition of Azure infrastructure, the AVD control plane, and a management layer like Nerdio, each solving part of the problem, but none owning the full experience.

The shift is subtle but significant: you move from **consuming a platform** to **managing an ecosystem**. This is where **Omnissa Horizon Cloud** differentiates itself, not as an add-on, but as a **complete platform**.

---

## 1. Application lifecycle: App packaging vs image sprawl

In AVD environments, application delivery typically relies on golden images, MSIX app attach (Low app compatibilty), and packaging pipelines, often resulting in image sprawl and complex update cycles.

With Horizon Cloud, **App Volumes** fundamentally changes the model. What App Volumes enables:
- Real-time app attachment at app launch
- Separation of apps from the golden image
- Instant updates without recomposing desktops
- Minimal golden image footprint (often 1–2 images)

**This is architectural simplification, not just operational improvement.** Nerdio can optimise image management. It cannot replace true application lifecycle.

---

## 2. User profiles: DEM vs profile containers

User state is one of the hardest problems in EUC, and one of the most visible when it goes wrong. Typical AVD approaches include:
- FSLogix profile containers
- Policies and scripting
- Intune-based configuration

These provide persistence, but limited control. With Horizon Cloud, **Dynamic Environment Manager (DEM)** delivers:
- Policy-driven user environment management
- Context-aware configuration (device, location, role)
- Granular application settings
- Fast, consistent logon/logoff experiences

**FSLogix stores profiles, DEM controls the user experience.**

---

## 3. User experience: Performance + Access, not one or the other

User experience isn’t just about performance, it’s about how users connect, authenticate, and interact with their digital workspace. It’s defined by two equally important factors:
1.	How users access their applications
2.	How those applications perform once connected

Most platforms focus on one. A mature EUC platform delivers both. This is where **Omnissa Horizon Cloud** differentiates itself.

### 3.1 The access layer: A true digital front door

Before performance even matters, users need a simple, consistent way to access their workspace. In AVD environments, access is typically spread across:
- Remote Desktop clients
- Web portals
- Multiple authentication entry points
- Azure-native conditional access layers

While functional, this often leads to fragmented access experiences, multiple user journeys and increased login friction. With Horizon Cloud, **Omnissa Access** provides a unified digital front door, delivering:
- A single portal for all apps and desktops
- Consistent experience across browser, mobile, and desktop
- Integrated authentication (including MFA and conditional access)
- Unified access to SaaS, web, and virtual applications

This provides a unique user experience, offering one place to go for everything, fewer prompts and context switches and a consistent experience regardless of device, location and whether workloads are running in the cloud or on-prem.

**Users don’t need to know where their apps live. They just know where to go.**

![](https://blog.beaugtech.com/assets/img/2026-04-01-Azurelab-replacement/Access-hub.png)

### 3.2 The performance layer: Blast Extreme

Once connected, performance becomes the defining factor of user experience. AVD relies primarily on Remote Desktop Protocol (RDP). While it has improved, it still presents limitations:
- Less adaptability in variable network conditions
- Limited optimisation for multimedia and graphics workloads
- Reduced control over protocol behaviour

Horizon Cloud introduces **Blast Extreme**, a purpose-built display protocol designed for modern workloads. Key advantages:
- Adaptive transport (intelligent UDP/TCP switching)
- Strong performance in high-latency or low-bandwidth conditions
- Optimised for video, audio, and GPU-intensive workloads
- Fine-grained tuning for bandwidth, quality, and responsiveness

### 3.3 Bringing it together: End-to-End user experience

Most solutions treat access and performance as separate concerns. Horizon Cloud integrates both into a single experience:
- Omnissa Access → how users connect
- Blast Extreme → how applications perform

By combining a digital front door with a high-performance protocol, Horizon Cloud helps:
- Simplify how users connect
- Optimise how applications perform
- Deliver a consistent experience across any environment

**The result is not just a working session. It’s a consistent, low-friction digital workspace experience.**

---

## 4. Integrated stack vs layered tooling

With AVD + Nerdio, you are combining multiple layers:
- Azure
- AVD
- Nerdio
- FSLogix
- Additional third party tooling for apps and policies

Each solves part of the problem. None own the full experience. With Horizon Cloud, everything is built-in:
- App lifecycle (App Volumes)
- User environment (DEM)
- Protocol (Blast)
- Brokering and access control
- Image lifecycle

**One platform. One control plane. One operational model.**

---

## 5. Lab & training use cases: Where it all comes together

Azure Lab Services worked because it delivered:
- Repeatability
- Isolation
- Simplicity

Recreating this with AVD requires:
- Careful architecture
- Multiple tools
- Ongoing operational effort

Horizon Cloud enables:
- Rapid provisioning from clean images
- Stateless or semi-persistent desktops
- Consistent environments across sessions
- Simple reset and rebuild cycles

Much closer to the original Lab Services experience, without sacrificing enterprise capability.

---

## 6. Future-proofing your EUC strategy: Hybrid by design, not by exception

One of the most overlooked questions is: **What happens when your requirements move beyond Azure?** Because they will. AVD is inherently Azure-centric. If requirements evolve, whether due to data sovereignty, mergers, or edge use cases, organisations are often forced into redesign rather than extension.

For example, a University may run training labs in Azure today, but require on-prem deployment for research environments with sensitive data tomorrow.

### 6.1. Horizon Cloud: Built for hybrid from day one

**Omnissa Horizon Cloud** takes a different approach. It is inherently hybrid, allowing you to:
- Deploy in Azure today
- Extend to on-premises tomorrow
- Expand into other cloud providers
- Maintain consistency across all environments

This approach enables true flexibility, allowing workloads to run where they make the most sense, whether in Azure, on-premises, or across multiple clouds, while maintaining a consistent architecture and reducing dependency on a single provider.

### 6.2 The bottom line
Microsoft’s recommendation of AVD + Nerdio is logical from an ecosystem perspective. But it optimises for Azure alignment, not necessarily best-in-class EUC capability.

AVD + Nerdio:
- Assembles a solution
- Relies on multiple layers
- Lacks depth in key EUC functions

**Omnissa Horizon Cloud delivers those capabilities natively, within a single platform.**

## 7. Final thought

Azure Lab Services didn’t just provide infrastructure. It delivered:
- Simplicity
- Control
- Repeatability

Rebuilding that with AVD + Nerdio is possible, but it comes at the cost of:
- Complexity
- Fragmentation
- Operational overhead

This is a moment to do more than replace. It’s a moment to rethink. Because the real decision isn’t “What replaces Azure Lab Services?” It’s **“What EUC platform do we want to standardise on for the next 5 years?**”

If you optimise for short-term alignment, AVD + Nerdio will get you there.

If you optimise for capability, consistency, and future flexibility, **Horizon Cloud** isn’t just an alternative. It’s the stronger long-term strategy.

So in short, if AVD + Nerdio is a solution you assemble, Horizon Cloud is a platform you standardise on.