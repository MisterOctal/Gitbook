---
icon: gear
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1766583599662
coverY: 0
coverHeight: 138
---

# Day 2 - VNet and Logistics

**Date:** 04.05.2026

**Core Concept:** Network Infrastructure and Fiscal Governance.

Establishing the virtual network architecture and financial guardrails was necessary to ensure the lab remains both technically isolated and cost effective. These logistics provide the foundation for scaling the lab without incurring unexpected cloud expenses.

***

## Section 1: SOC-Lab-Vnet Infrastructure

The core network infrastructure was deployed using a single Virtual Network named SOC-Lab-Vnet. This network is segmented into five specific subnets to facilitate micro-segmentation and traffic control.

<figure><img src="../.gitbook/assets/image (319).png" alt="" width="563"><figcaption></figcaption></figure>

The subnets for management, analysis, and the honeypot were associated with the Network Security Groups created on Day 1. The landing and spare subnets provide entry points and expansion room for the lab.

| Subnet Name       | Address Range | NSG Association |
| ----------------- | ------------- | --------------- |
| subnet-landing    | 10.0.1.0/24   | None (Pending)  |
| subnet-management | 10.0.2.0/24   | Management-Zone |
| subnet-analysis   | 10.0.3.0/24   | Analysis-Zone   |
| subnet-honeypot   | 10.0.4.0/24   | Honeypot-Zone   |
| subnet-spare      | 10.0.5.0/24   | None (Pending)  |

***

## Section 2: Subnet Justification

The decision to utilize five subnets is based on the requirement for complete logical separation of duties.

Segmenting the honeypot from the analysis zone ensures that a compromise of a target VM does not lead to an immediate compromise of the SIEM stack. The landing subnet serves as a dedicated zone for initial ingress or traffic redirection.

Separate subnets also allow for more granular routing and the potential implementation of Network Virtual Appliances or firewalls in the future. The spare subnet provides the flexibility to grow the lab without re-architecting the primary address space.

The various additional security features were also disabled, as they are extremely costly and not necessary for a home Azure lab.

***

## Section 3: Budget and Fiscal Monitoring

To maintain fiscal responsibility, a monthly budget of $8 was configured at the subscription level. This ensures that the lab remains a low cost learning environment and provides protection against accidental resource over-provisioning.

<figure><img src="../.gitbook/assets/image (320).png" alt="" width="563"><figcaption></figcaption></figure>

Automated alerts were set up to trigger at specific consumption thresholds to provide real time visibility into spending. These notifications are sent to both a personal email and a student email address for redundancy.

***

## Conclusion

Day 2 successfully transitioned the project from initial configuration files to a live network environment. The deployment of the VNet and the implementation of budget alerts ensure that the lab is operational, organized, and financially controlled.

I have also uploaded the scrubbed versions of these configurations to my GitHub repository to serve as a proof of methodology and to document the build process.

The project is now ready for the deployment of virtual machines into their respective security zones.
