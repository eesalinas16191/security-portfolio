🔒 Project Title: Virtual Lab Privileged Access Audit & Hardening Report.

The Audit Path

1. Finding: Baseline
   Let's take a look at the findings revealed from the audit regarding the resources utilized. One would suspect that if looking for convenience one would choose to audit through using the portal's Access Control blade. However, this is not the optimal method for inspections. Instead, the more appropriate method would be to look at role assignment exports either through a CSV or JSON format.

<img width="1366" height="768" alt="Azure Lab 3 SS1" src="https://github.com/user-attachments/assets/08dbafd5-5750-41cc-bcab-aa5bba991c93" />

Looking at the CSV format versions of the role assignments was where the audit found what appears to be an account with a suspicious amount of privileged access over a vast number of resources. This account was found the be the Owner over these resources which goes against maintaining secure security configurations. Another one of the violations going against the security practices is that the export is that the groups are shown but the members inside of the groups aren't. This can present all sorts of other issues that can make it difficult to track and manage accounts and over allocating powerful access to other accounts. 


 2.  Azure CLI
  So in order to get the full picture of audit I went to the Azure command line to find any hidden or privileged accounts. 
  
  <img width="1366" height="768" alt="Azure Labe 3 SS 2 3" src="https://github.com/user-attachments/assets/2c200c3c-a42a-46b9-a82c-a5bb385c5770" />

<img width="1366" height="768" alt="Azure Lab 3 SS 2 2" src="https://github.com/user-attachments/assets/be665da2-8655-45ac-a832-de099e11fc29" />

I used the specific commands necessary for me to expose any unknown deleted accounts that can be hidden from using regular auditing methods.

<img width="1366" height="768" alt="Azure Lab 3 SS 1 2" src="https://github.com/user-attachments/assets/1e24460f-6ab4-496c-a0b0-095524a0c0c2" />

After observing the export and specifying the scope of the search I found an assignment with the principalName completely gone. This strange finding meant that this had been a deleted account that kept it's permissions. 


3. Azure Resource Graph with KQL
The next step in the audit came down to using the Azure Resource Graph and using the specific KQL query to look for any active assignments. 

<img width="1366" height="768" alt="Screenshot from 2026-09-24 17-16-01" src="https://github.com/user-attachments/assets/b91cc75f-2022-4c40-8593-dd57b1fa1bd4" />

Using the principal ID of the deleted account in the query led to the findings of the active assignments present in the portal. However, in order to see the eligible role assignment that meant the audit had inspect the PIM side of the portal. 

4. Privileged Identity Management export
Inspecting the Privileged Identity Management portion of the audit meant examining the Azure resources under the management blade of the portal.

<img width="1366" height="768" alt="Azure Lab 3 SS4 1" src="https://github.com/user-attachments/assets/4ad2a844-530e-4a48-bfac-f954b00032d8" />

Selecting the specific subscription and resources group leads to the Overview portion of the sequence where further inspection of the assignments tab would help me uncover more of the eligible role assignments. 

<img width="1366" height="768" alt="Azure Lab 3 SS4 2" src="https://github.com/user-attachments/assets/15029538-caf4-4fb8-8c48-4b8cf493ddd2" />

This further deepens the audit into inspecting the specific details of the roles by exporting the the audit report. 

<img width="1366" height="768" alt="Azure Lab 3 SS4 3" src="https://github.com/user-attachments/assets/73ecfef4-3b9a-44b3-8224-4b2f49af3226" />

<img width="1366" height="768" alt="Azure Labe 3 SS4 4" src="https://github.com/user-attachments/assets/f6910d67-bb28-4a7e-99c4-9d137d2e9538" />

5. The Hunt
These inspections led to more the findings of another unrecognized account that remained concealed. To help identify this hidden account I had to activate an eligible Azure role on my reader account. 

<img width="1366" height="768" alt="Azure Lab 3 SS5 1" src="https://github.com/user-attachments/assets/186619ba-b3ee-4cc3-934d-12f7b9820ec5" />

<img width="1366" height="768" alt="Azure Lab 3 SS5 2" src="https://github.com/user-attachments/assets/a7729fda-b494-4457-813a-7cec83e85670" />

After the activation of the necessary eligibility role it gave me the ability to find any suspicious or hidden resource groups. I then focused on digging deeper on a resource group that was hidden among the other to download the role assignments. After observing the results of the CSV export I found the necessary information regarding the hidden resource group and it's Owner. 

<img width="1366" height="768" alt="Azure Lab 3 SS5 3" src="https://github.com/user-attachments/assets/5e7d081c-66dc-4039-a8b2-98d3de552277" />



   
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
