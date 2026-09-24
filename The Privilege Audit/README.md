🔒 Project Title: Virtual Lab Privileged Access Audit & Hardening Report.

The Audit Path

1. Finding: Baseline
   Access Control (IAM) blade and the role assignment export. The no-tooling baseline. Shows every principal with access at a scope, including inherited grants. Blind spot: it lists groups but not their members, and it tends to bury the orphaned "Identity not found" assignments.

   Azure CLI. Same data, but scriptable and repeatable, and it surfaces what the portal hides: an assignment whose principalName comes back empty. That is a deleted account whose permissions were never revoked. Blind spot: one scope per run.



Azure Resource Graph with KQL. Checks the entire tenant in a single query instead of one scope at a time. Blind spot: it only sees ACTIVE assignments.



Privileged Identity Management export. The only method that shows eligible versus active, plus the activation history of who elevated, when, and why. Blind spot: it does not cover standing assignments that were never brought under PIM.



The Hunt. Synthesis, word of the day, fancy word for combining knowledge of different facets of Azure to answer a question. You activate an eligible role and go find the over-provisioned account yourself, with no instructions.
   
   
| Method               | Sees                                        | Misses                              |
|----------------------|---------------------------------------------|-------------------------------------|
| IAM blade / export   | Active assignments at scope, inherited      | Group members, orphaned principals  |
| Azure CLI            | Same, plus null principalName (orphans)     | One scope per run                   |
| Resource Graph (KQL) | Whole tenant in one query                   | Eligible assignments                |
| PIM export           | Eligible vs active, activation history      | Assignments outside PIM             |

Write it as an AUDIT REPORT, not an incident report. The write up might end up looking similar to earlier ones, but it's a different structure:





Scope and methodology. What tenant, what access level you had (Reader, plus one PIM-eligible role), which four tools you used and why.



Findings, severity-ranked. Describe the CLASS of each finding, not the value: an orphaned role assignment tied to a deleted principal, redundant Owner grants applied across many scopes, apparently by script, standing privileged access that should have been PIM eligible instead.



Why the orphan matters at all? Obviously try to say this in your own words because it's one of the most senior-sounding things in the whole write-up: a role assignment tied to a deleted principal is not just part of a mess that needs clean up at some point. Azure stores the GUID, not the name. If anything ever recreates a principal with that object ID, the permissions are sitting there waiting. An account that has potential for abuse that is a dormant account with access that nobody is monitoring.



Recommendations: revoke orphaned assignments. Replace redundant Owner grants with the narrowest job-function role at the narrowest scope. Move standing privileged access to PIM-eligible with MFA, justification, and time limits. Assign to groups rather than users. Run this audit on a quarterly cadence and treat the output as a ticket queue so it actually gets done.
