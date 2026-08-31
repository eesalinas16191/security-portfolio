# security-portfolio
# Security Portfolio: [Edson Salinas]

Documented cloud security investigations, built in a live Azure tenant
(Mad Hat Labs, a multi-user training environment).

Target role: SOC Analyst / Security Analyst
Currently: [CNA] | [Harlingen]
Contact: [edsonsalinas281@gmail.com] · [www.linkedin.com/in/edson-salinas-399b281a9]

## Investigations

| 1 |

# [Operation Dead Deploy, "Investigated Unauthorized Usage of Resources"]

## Scenario
A recent intern had left a deployment running after being given access to run a temporary experiment which led to unexpected problems. The investigation had concluded that mismanaged policy assignments was the reason as to why the intern was able to deploy the resource group even when it violated the rules. 


Situation involved myself investigating and locating an out of the ordinary resource group which was found to be unauthorized. It did not adhere to the Microsoft Naming Convention which made it stick out, prompting further investigation into it's origin.  

## Environment
"Cloud computing, Cloud services, live multi-user Azure training tenant, Reader access."

## Investigation
The core. Numbered steps IN YOUR OWN WORDS: what you looked at, what you found, what you concluded at each step. 6 to 12 screenshots of meaningful moments (portal views, query results, before/after).
Step 1: I first focused on locating the origin of the mishandled deployment by scanning the resource groups and looking for any of them that did not adhere to the Microsoft Naming Convention principles. After searching for a bit, I found a resource group name that was out of the ordinary which prompted further investigation. 
<img width="1600" height="900" alt="Untitled" src="https://github.com/user-attachments/assets/e5c9aaf1-47e4-4ce5-a195-1a580bbcf7b5" />
I concluded that because the intern didn't commit to the proper naming practices of resource groups, searching for the issue's name became easier to identify.
Step 2: I then began investigating further into what was in the resource group and after closely monitoring the tags listed I found more details regarding the origin of the deployment and a further lack of proper naming practices aside from the intern-flag. 
<img width="1600" height="900" alt="Step 2 Screenshot" src="https://github.com/user-attachments/assets/c5027350-aaa9-4986-92e9-c6941104a3e3" />
<img width="1600" height="900" alt="Step 2 1 Screenshot" src="https://github.com/user-attachments/assets/1ccc1c5a-eb60-4af1-9945-7c55c5e4fa9d" />
<img width="1600" height="900" alt="Step 2 2 Screenshot" src="https://github.com/user-attachments/assets/53b04af8-e106-4976-9cc2-0dcbb425de56" />
I concluded that the intern's lack of compliance according to the Microsoft Naming Convention practices in the deployment process prompted further investigation because of how easily he was able to bypass the policy assignments.  
Step 3: The mystery then led to me searching in the deployments area of the resource group I was looking at and found even more details including the name, status and timestamp of the deployment beginnings. 
<img width="1600" height="900" alt="Step 3 Screenshot" src="https://github.com/user-attachments/assets/eb10a595-5cbe-42f0-ac1d-feb67fee7f15" />
<img width="1600" height="900" alt="Step 3 1 Screenshot" src="https://github.com/user-attachments/assets/6b80471b-8289-4e29-a187-6461f2a13497" />
I concluded that further research into who created the deployment had me suspecting this was more of a configuration issue that led to the intern's practices being allowed to sneak by.  
Step 4: 






## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."

| 2 | The Stolen Identity | App registration attack kill chain (Entra ID) | coming, week 2 |
| 3 | Privilege Audit | RBAC and least privilege | coming, week 3 |
| 4 | Spin Up and Lock Down | Compute attack surface | coming, week 4 |
| 5 | Network the Operative | Network segmentation | coming, week 5 |
| 6 | Bucket Looting | Storage exposure hunting | coming, week 6 |
| 7 | Find the Anomaly | Log analysis and KQL | coming, week 7 |
| 8 | Hunt the Threat | SIEM operations (Sentinel) | coming, week 8 |
| 9 | Score the Tenant | Cloud security posture | coming, week 9 |
| 10 | The Breach (capstone) | Full incident investigation | coming, week 10 |
