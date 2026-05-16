---
icon: screwdriver-wrench
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 1 - Lab Setup

**Date:** 16.05.2026

**Core Concept**: Financial Governance and Operational Visibility

Day 1 is dedicated to establishing the safety net for the lab. Since we are operating on a limited credit pool, it is vital to have the budget alerts and a monitoring dashboard active before any virtual machines are provisioned. This ensures that we have immediate visibility into costs and can easily manage the power states of our resources to prevent credit exhaustion.

***

## Section 1: Budget and Cost Management

The most important step in any cloud project is the financial kill switch. We have implemented a multi-tiered alert system to monitor the $100 credit.

<figure><img src="../.gitbook/assets/image (410).png" alt="" width="563"><figcaption></figcaption></figure>

* **Hard Budget Limit:** A budget was created with a threshold of $10 per month. And smaller alerts were set every $2.5. This forces us to be intentional with our testing windows.
* **Forecasted Alerts:** We enabled alerts for when Azure predicts we will hit 100% of our budget. This is the early warning system that catches runaway processes before they actually spend the money.
* **Email Notification:** Alert triggers are mapped to my personal and college emails, ensuring we are notified within hours of any unusual billing activity.

***

## Section 2: The SOC Operations Dashboard

<figure><img src="../.gitbook/assets/image (413).png" alt=""><figcaption></figcaption></figure>

We have also created a dedicated Resource Group (RG-SOC-Lab) to house all assets. Since the environment has 0 active VMs currently, the dashboard is being built as a manifest of services to be pinned during the deployment phase.

**Planned Dashboard Components:**

1. **Cost Management (The Burn Rate):**
   * **Target:** Accumulated Costs Graph.
   * **Action:** Pin from the Cost Analysis blade once the first cent is spent.
   * **Purpose:** High level visibility of credit consumption.
2. **Compute Control (The Kill Switch):**
   * **Target:** All Resources list (filtered to RG-SOC-Lab).
   * **Action:** Pin the Resource Group overview page.
   * **Purpose:** One click access to start or stop all VMs to ensure they are deallocated.
3. **Analysis Gateway (The SIEM Hub):**
   * **Target:** Log Analytics Workspace (LAW).
   * **Action:** Pin the LAW overview page once created.
   * **Purpose:** Quick access to the KQL query editor and Sentinel incident logs.
4. **Health Monitoring (The Global View):**
   * **Target:** Service Health Map.
   * **Action:** Pin the world map view from the Service Health blade.
   * **Purpose:** Distinguish between local configuration errors and Azure platform outages.

***

## Section 3: Operational Guardrails

Beyond the budget, we have established the "On-Demand" rules of engagement for this lab.

* **The Deallocation Rule:** We will never simply "Shut Down" from within the VM guest OS. We will always use the Azure Portal to ensure the hardware is deallocated, which stops the hourly compute charges.
* **Auto-Shutdown Policy:** A fail-safe auto-shutdown is configured for 12:00 AM local time. This acts as our insurance policy in case we forget to manually stop the lab after a night session.

***

## Conclusion

With the budget locked in and the dashboard ready, Day 1 is complete. We now have a safe environment where we can deploy resources with the confidence that we won't accidentally wake up to a $0 balance. Tomorrow, we move into Day 2: Virtual Networking and Security Group configuration.
