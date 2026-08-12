---
icon: clipboard-check
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 22 - Conclusion

**Date:** 12.08.2026

**Core Concept**: Project closure, findings synthesis, and operational retrospective

The Azure SOC homelab project concluded today with resource group deletion and final data archival. Over 99 days of operational honeypot activity generated 46,000+ security events across 317 unique source IPs, resulting in comprehensive threat intelligence collection and hands-on detection engineering experience. This writeup synthesizes key findings, operational lessons, and architectural decisions for future iterations.

***

## Section 1: Deliverables Archived

All project artifacts were successfully recovered and archived for long-term reference:

**Cowrie Honeypot Logs**: 17.9 MB compressed archive (cowrie-2026-all.zip) containing complete JSON event logs from May 24 - Aug 2, 2026. Includes \~46,000 events spanning reconnaissance, credential access, command execution, and malware deployment attempts.

**KQL Query Reference**: Comprehensive query library (11\_08\_2026.kql) documenting 16 MITRE ATT\&CK techniques detected across the dataset, with executable queries and result commentary for reproducibility.

**Lab Writeups**: 21 day-by-day technical writeups documenting infrastructure decisions, deployment challenges, analysis methodology, and threat findings. Each writeup maintains honest acknowledgment of limitations and design trade-offs rather than glossing over failures.

**GitHub Public Repository**: Full honeypot dataset, custom configuration scripts, and analysis documentation uploaded for public reference and reproducibility by security researchers.

***

## Section 2: Threat Findings Summary

The honeypot captured evidence of at least three distinct threat actors operating at different sophistication levels:

**Tier 1: Sophisticated Malware Campaign (91.199.133.133)**

A single session revealed a professionally-engineered multi-stage payload deployment chain with 16 distinct MITRE ATT\&CK techniques. Key characteristics: architecture fingerprinting, redundant delivery methods (wget, curl, /dev/tcp socket), hex-encoded obfuscation, anti-sandbox behavior testing, GPU/CPU profiling for cryptominer targeting, and file permission manipulation to bypass hardening. VirusTotal independently confirmed files (real\_x86\_64, deploy.sh) served from this IP with double-digit detection counts. This represents genuine threat actor infrastructure, not script-kiddie activity. External threat intelligence lookups (VirusTotal, AbuseIPDB) were performed manually via web interface, consuming project time. A future iteration should integrate these as APIs into the analysis pipeline for automated IOC enrichment and bulk scoring.

**Tier 2: Distributed Credential Scanning Botnet (45.153.34.x, 103.82.210.181, etc.)**

High-volume, low-sophistication credential spraying from multiple IPs within coordinated network ranges. 103.82.210.181 alone attempted 1431 logins. Dictionary passwords (123456, 123, 1234) tried across 40-70+ usernames per password. No post-exploitation sophistication observed. VirusTotal confirms 45.153.34.x associated with destoryed.exe malware distribution. This represents automated commodity scanning tools, likely rented botnet access. Bulk scoring of all source IPs against VirusTotal via API would have been more efficient than spot-checking individual IPs via web interface.

**Tier 3: Individual Actor (58.235.123.214)**

A single session showing intermediate sophistication: successful login, privilege escalation attempts (su root), C2 contact attempt (103.207.85.8 confirmed malicious on VirusTotal), and opportunistic command execution. Failed at privilege escalation and likely abandoned the session. Represents either a human operator or semi-autonomous exploitation tool.

**Data Volume**: 40 successful logins across the entire dataset vs. \~7,600 failed attempts. 203 commands executed post-compromise. Attack volume dried completely after Aug 2, suggesting honeypot IP rotated out of active scanner lists.

***

## Section 3: Technical Architecture Review

**What Worked Well**

The decision to host honeypots in Azure while running the SIEM backend (ELK) on the same platform initially seemed sound for ease-of-use, but proved operationally inefficient. The three-tier original design (Cowrie SSH honeypot, Windows RDP honeypot, ELK stack) captured comprehensive attack telemetry and validated both detection engineering principles and log pipeline concepts end-to-end. NSG segmentation effectively isolated administrative access while exposing bait ports to the public internet without introducing lateral movement risks. Custom initialization and SSH migration scripts proved reusable across multiple VMs, reducing configuration drift. Docker containerization for Cowrie provided clean isolation and rapid recovery paths.

**What Didn't Work**

The Windows honeypot generated RDP brute force telemetry but never captured meaningful post-exploitation activity due to platform-level hardening preventing successful decoy account access. This meant 48 hours of compute cost yielded only username/IP intelligence, which was correctly decommissioned on Day 17. The full ELK stack consumed \~$1.50/day in compute costs for an analysis backend that proved redundant once Sentinel/Log Analytics became available through the same subscription. In hindsight, running ELK locally would have preserved the same hands-on learning while eliminating cloud compute waste. The Azure Monitor Agent pipeline's Fluent Bit tail-detection limitation and timestamp corruption forced a workaround-dependent workflow (restart-to-ingest), reducing automation reliability. The decision to backfill 3 months of historical logs into Sentinel, while data-preserving, was ultimately unnecessary given the timestamp corruption made historical timeline analysis impossible anyway.

**Budget Reality**

Started with $100 Azure student credit. Spent roughly $90 over 99 days, primarily on compute (honeypot VMs), storage (managed disks), and brief periods running both ELK and Sentinel simultaneously. Final $10 remaining. The project was financially viable but only through aggressive cost discipline: stopping VMs instead of deleting (retains logs, saves compute), ruthlessly decommissioning low-value infrastructure (Windows honeypot, ELK), and accepting natural attack volume decline rather than artificially extending collection periods. A production homelab would benefit from local ELK + cloud-only honeypots, inverting the cost distribution.

***

## Section 4: Operational Lessons Learned

**Logging Pipeline Complexity vs. Utility Trade-off**

Building a custom log ingestion pipeline (Cowrie to Filebeat to Logstash to Elasticsearch to Kibana) provided deep hands-on experience with each component, debugging data flow, and understanding the operational costs of running each tier. However, the same pipeline implemented via a cloud-native path (Cowrie to AMA to Log Analytics Workspace to Sentinel) delivered equivalent analytical capability with lower overhead, though with its own quirks (timestamp corruption, regional provider gaps, file-tailing limitations). Neither approach was "better" objectively; the trade-off is between learning operational complexity vs. accepting vendor-specific limitations.

**Manual Infrastructure Deployment vs. Infrastructure-as-Code Discipline**

Much of the early lab work (Days 1-10) involved manual Azure Portal clicks: VNet creation, NSG rule configuration, VM provisioning, disk attachment. This provided visual familiarity with the platform but was error-prone (did we really specify the right subnet?) and consumed project time that could have been spent on analysis. Bicep templates were only created late in the project for the final decommissioning phase. A future iteration should enforce CLI-based or IaC-based deployment from Day 1, eliminating portal clicks entirely. This would reduce human error, enable rapid iteration via version control, and provide documentation-as-code for each infrastructure decision. The time investment in learning Bicep or Terraform upfront would pay dividends across the full 99-day span.

**Honeypot Configuration Matters More Than Volume**

The initial Cowrie configuration rejected all login attempts, which captured reconnaissance patterns but zero post-exploitation behavior. Switching to weak but realistic credentials (root/123456, admin/admin123) on Day 11 unlocked genuine attack chain data. The sophistication of the attacker (malware dropper) remained constant; only the honeypot's permissiveness changed. Future iterations should prioritize configuration quality over collection duration.

**Inconsistent Lab Discipline Impact**

Work intensity varied significantly across the 99-day span. Days with focused effort (Days 18-21, intensive KQL and MITRE mapping) produced disproportionate value compared to days where attention was split with coursework or personal obligations. Gaps between lab sessions sometimes meant VMs sat idle for days without activity logging, consuming credit with minimal ROI. A future iteration should establish weekly commitments upfront (e.g., Monday analysis sessions, Wednesday infrastructure maintenance) rather than sporadic engagement. This consistency would maintain honeypot placement in active scanner rotation lists, reduce idle compute costs, and ensure continuous analytical workflow rather than start-stop patterns.

**Documentation Discipline Pays Dividends**

Maintaining day-by-day writeups forced continuous reflection on decisions, limitations, and failures. This prevented narrative-stitching (inventing explanations for unexplained gaps) and caught genuine operational problems early (timestamp corruption, Fluent Bit reliability). The discipline of admitting "this didn't work" or "we don't know why this happened" proved more valuable than polished-looking but misleading summaries. Threat analysis is only credible when limitations are transparent.

**Attack Volume Decline Was Real**

After Day 9 (public exposure), attack traffic peaked around Days 26-27 (Jul 26-27 mega-event with 117MB of activity in a single day), then steadily declined through August. By August 2, the honeypot was receiving minimal traffic despite the VM remaining online, NSG rules open, and network connectivity intact. This suggests honeypot IPs have natural lifecycles in scanner rotation lists, or the infrastructure that initially hit the honeypot moved on to other targets. Sustainable long-term collection would require either IP rotation or permanent enrollment in active threat campaigns.

***

## Section 5: MITRE ATT\&CK and Threat Intelligence Value

The 16 MITRE ATT\&CK techniques detected across the dataset map to a coherent attack chain:

**Reconnaissance → T1592, T1580, T1083, T1087** (architecture and resource profiling)

**Initial Access → T1110.003, T1110.004** (credential spraying and stuffing)

**Persistence → T1098** (credential reuse across sessions)

**Defense Evasion → T1222.002, T1622** (permissions manipulation and sandbox detection)

**Command and Control → T1071.001, T1008, T1105, T1571** (HTTP delivery with fallback methods)

**Execution → T1651, T1204.002** (hex-encoded staging and script execution)

**Impact → T1496** (inferred cryptominer deployment based on GPU profiling)

This progression represents the actual attack lifecycle observed in security incidents, not a theoretical taxonomy. The data can directly inform detection rule development, incident response playbooks, and threat hunting queries.

***

## Section 6: Mistakes Made

**Timestamp Corruption Acceptance**

Backfilling 46,000 historical events into Sentinel when the ingestion pipeline was known to corrupt timestamps was operationally correct (data preservation) but analytically counterproductive. The AMA/Fluent Bit limitation meant all historical events landed stamped with ingestion time, making timeline-based analysis of the first 3+ months impossible. A smarter approach: accept the limitation upfront and only ingest fresh data going forward, or maintain the raw JSON export as the source-of-truth archive separately.

**ELK Stack Cloud Hosting Decision**

Running a full ELK stack in Azure to analyze honeypot logs running on the same platform seemed efficient until cost accounting. The combined compute+storage for the ELK VM eclipsed the honeypot itself. Local ELK would have cost nothing (laptop CPU cycles) while providing identical hands-on learning. This decision should have been driven by learning objectives (do I need to practice ELK operational deployment?) rather than convenience.

**Manual Portal Configuration**

Early infrastructure work (Days 1-10) relied on Azure Portal UI for VNet creation, NSG configuration, and VM provisioning. While this provided visual familiarity, it was time-consuming, error-prone, and created undocumented configurations. By the time Bicep templates were authored for decommissioning, the entire lab architecture should have been code-defined from Day 1. This would have reduced human error, enabled rapid iteration, and provided version-controlled infrastructure documentation.

**Not Rotating Honeypot IPs**

Once attack traffic peaked and declined, the natural response would have been to delete the public IP, generate a fresh one, and resume collection. Instead, the decision was to let the honeypot sit idle at minimal traffic. The fixed IP likely aged out of active scanner rotation lists. Future iterations should plan for periodic IP rotation or accept that collection windows have natural lifespans.

**Manual Threat Intelligence Lookup**

VirusTotal and AbuseIPDB checks were performed manually via web interface throughout the project. Each IP enrichment required a manual lookup, wait for page load, and documentation of results. Bulk API access would have enabled automated scoring of all 317 source IPs in minutes rather than hours of manual triage. This represents a clear gap between operational practice and production tooling.

**Insufficient Attacker Telemetry Preservation**

While the command chains were captured, richer context (exact timing, network payload details, process creation chains) was limited by Cowrie's JSON event structure. A supplementary tcpdump or network-level IDS alongside the honeypot would have captured payload binaries and C2 communications unfiltered. Cost trade-off prevented this in the current iteration, but it's a known gap.

**Inconsistent Work Schedule**

Project intensity varied significantly. Days with focused effort yielded disproportionate results compared to days split with coursework or personal obligations. Gaps between lab sessions sometimes lasted 3+ days, during which VMs consumed credit while sitting idle. A committed weekly schedule (e.g., 4 hours Tuesday, 4 hours Thursday) would have maintained consistent analytical workflow and prevented the mid-project slowdowns that allowed attack volume to naturally decline.

***

## Section 7: What Was Learned

**Technical Skills Cemented**

* KQL query patterns for security analysis (summarize, join, time-series aggregation)
* Azure networking primitives (VNets, NSGs, network flow, resource groups)
* Filebeat/Logstash/Kibana log pipeline architecture and failure modes
* Microsoft Sentinel analytics rule configuration and incident correlation
* Azure Monitor Agent deployment, DCR configuration, custom table schema design
* Docker containerization for security tools (Cowrie, ELK stack)
* Bicep infrastructure-as-code for resource templating and cleanup

**Threat Intelligence Insights**

* Commodity credential scanning is distributed, low-sophistication, high-volume
* Sophisticated malware infrastructure exhibits deliberate environmental profiling (architecture, GPU, sandbox detection)
* Successful login rates are extremely low for generic honeypots (\~0.5% conversion), but the sessions that succeed are operationally valuable
* Attack infrastructure shows clear economic optimization: automated scanning < manual exploitation << sophisticated payload deployment
* Post-compromise behavior is where genuine threat actor TTPs emerge; pre-compromise activity is mostly noise

**Operational Discipline**

* Cost discipline enables long-term projects on limited budgets (the $100 credit sustained 99 days)
* Honest documentation of failures and limitations builds credibility more than polished-looking summaries
* Automation reduces configuration drift and human error (custom init scripts, Bicep templates)
* Monitoring and alerting (Uptime Kuma for honeypot health) prevent silent failures
* Version control for configurations enables repeatability and serves as future reference

***

## Section 8: Future Iteration Planning

**Next Homelab Should Prioritize**

1. **Infrastructure-as-Code First**: All Azure resources deployed via Bicep or Terraform from Day 1. No portal clicks after initial environment setup. This eliminates drift, enables rapid teardown/rebuild cycles, and serves as living documentation of all infrastructure decisions.
2. **Automated External Enrichment**: Integrate VirusTotal and AbuseIPDB APIs into the analysis pipeline. Every extracted IOC (source IP, malware hash, domain) should be automatically scored and enriched without manual triage. This removes a major time sink and ensures comprehensive coverage of all detected indicators.
3. **Local SIEM Backend**: Run ELK or Splunk on personal infrastructure (laptop, local VM server). Cloud should only host honeypots and log aggregation endpoints.
4. **Consistent Lab Schedule**: Establish fixed weekly commitments (e.g., Tuesday/Thursday analysis sessions, Friday infrastructure review) rather than sporadic engagement. Consistency maintains honeypot visibility in active scanner lists and prevents idle-cost waste.
5. **IP Rotation Strategy**: Plan for quarterly IP changes to maintain placement in active scanner rotation. Track aggregate statistics across rotations rather than treating each IP as isolated.
6. **Richer Telemetry Collection**: Supplement Cowrie with tcpdump, network-based IDS, and filesystem monitoring via auditd to capture full attack chains beyond command execution.
7. **Multi-Region Deployment**: Honeypots in different geographic regions attract different threat actors and scanning patterns. Requires modest cost overhead but yields better dataset diversity.
8. **Automated Attack Simulation**: Synthetic traffic injection (red team activity) during slow periods to maintain detection rule tuning capability across the collection window.
9. **Formal Detection Rule Development**: Instead of ad-hoc KQL exploration, systematically develop and test detection rules against known attack patterns, measuring false positive/negative rates.
10. **Timeline Analysis**: Fix the timestamp corruption problem upfront. Either accept cloud ingestion limitations and maintain raw export archives, or use a different ingestion path that preserves embedded timestamps.

***

## Section 9: Core Values Demonstrated

**Operational Honesty**

This project never claimed perfect results or sanitized away problems. Day 18's writeup explicitly documented the AMA pipeline's timestamp corruption limitation and the Fluent Bit tail-detection workaround rather than pretending the pipeline was flawless. Day 17 acknowledged budget pressure drove decommissioning decisions rather than framing them as purely technical. This builds credibility for future work.

**Pragmatic Engineering**

Perfect infrastructure would have included Azure Firewall, Bastion, DDoS Protection, and additional monitoring. Instead, the project accepted known limitations (NSGs only, no Bastion) in exchange for staying within a $100 budget. This reflects real-world constraints most security practitioners face: optimal is often infeasible, pragmatic trade-offs are mandatory.

**Process Over Polish**

Early days involved manual configuration and frequent mid-project architecture changes (decommissioning Windows VM, pivoting from ELK to Sentinel). While these pivots were necessary and well-documented, they consumed time and created inconsistency in deployment methodology. Future work will emphasize IaC discipline and upfront architectural decisions to reduce ad-hoc changes and maintain consistency across the full project lifecycle.

**Continuous Learning Orientation**

The project prioritized understanding each component deeply (why does Logstash parse this way? how does Filebeat tail files?) over achieving a polished final product quickly. This extended timeline but built transferable knowledge applicable to other SIEM platforms, cloud providers, and log aggregation scenarios.

**Data-Driven Analysis**

Threat conclusions were grounded in query results and external corroboration (VirusTotal, AbuseIPDB) rather than speculation. Session a6ae8bbbe44e was flagged as sophisticated only because its command patterns were genuinely different from the bulk commodity traffic, not because of narrative convenience.

***

## Section 10: Skills Portfolio Value

For job applications and portfolio building, this project demonstrates:

* **Detection Engineering**: Built and tested KQL analytics rules against real attack data
* **Cloud Infrastructure**: Designed and implemented secure network architecture on Azure, managed compute costs
* **Threat Intelligence**: Analyzed real malware deployment chains, corroborated findings against external threat databases
* **Log Analysis & SIEM**: Hands-on experience with ELK stack and Microsoft Sentinel platforms
* **Operational Security**: Managed secrets (SSH keys, API credentials), segmented network access, maintained audit trails
* **Technical Communication**: Documented complex technical work in clear, honest prose without oversimplification or narrative-stitching

The day-by-day writeups serve as evidence of process and thinking, not just final results. Hiring managers reviewing this project can trace decisions, mistakes, and learning across the full timeline, which is more credible than a polished-after-the-fact summary.

***

## Section 11: Resource Group Deletion

Azure CLI command executed at 12:00 UTC on 12.08.2026:

```bash
az group delete --name RG-SOC-LAB --yes --no-wait
```

This initiated asynchronous deletion of all resources: VMs, managed disks, network interfaces, virtual networks, NSGs, public IPs, Log Analytics Workspace, and associated storage. Deletion typically completes within 5-10 minutes. All residual cloud costs cease immediately.

Local copies of all project artifacts (writeups, KQL files, Cowrie logs archive, Bicep templates) retained for offline reference and future iteration planning.

***

## Conclusion

The SOC homelab project demonstrates that meaningful security operations experience is achievable on a constrained budget with disciplined scoping and honest documentation. The 46,000 events captured across 99 days provided genuine threat intelligence, hands-on detection engineering practice, and operational lessons applicable to production SOC work. The identified tier-3 threat actor with a professional multi-stage payload chain and the distributed tier-1 credential scanning botnet represent real attack infrastructure, not simulated scenarios.

Future iterations will benefit from lessons learned: local SIEM backends for cost efficiency, honest upfront acknowledgment of platform limitations, periodic honeypot IP rotation for sustained collection, and richer telemetry capture beyond command-level logging. The core discipline of transparent documentation of successes and failures remains the most portable skill developed across this project.

Project duration: May 16 to August 12, 2026 (99 days). Total budget consumed: approximately $90 of $100 Azure student credit. Final artifact count: 21 technical writeups, 2 comprehensive KQL query libraries, 1 archived honeypot dataset (17.9 MB), 1 public GitHub repository, measurable threat intelligence on 3 distinct threat actors, and operational knowledge directly applicable to SOC analyst and threat intelligence roles.

This closes the Azure SOC homelab project... For now.
