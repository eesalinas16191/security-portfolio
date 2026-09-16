# [The Stolen Identity"]

## Scenario

In this scenario a malicious agent was granted access to an enterprise app through using a multi step OAuth consent-phishing attack which led to them stealing the necessary tokens necessary for them to bypass the authentication safeguards. They made several configurations inside the 2 app registrations for them to remain in control and reduce suspicion of their activity. This investigations answers the question of what the attacker was able to accomplish through clues left behind in the Azure portal. 



Reconstructed a five-stage OAuth consent-phishing kill chain in a live Azure tenant through forensic analysis of two linked app registrations."

## Environment

"Cloud computing, Cloud services, live multi-user Azure training tenant, Reader access."

## Investigation

These five steps listed below will outline how the investigation was orchestrated.

Step 1 ENTRY: I first investigated this breach from the source of it all. That source being a suspicious link being utilized to phish an employee who mistakenly and unknowingly granted permission to a malicious actor during this attack. I went looking at what the attacker was targeting which was a specific enterprise app registration. Looking through the app registration page is what led me to the first clue in the Branding & Properties section of the legacy app. After looking at the internal notes compartment of that section gave it made me more confidence that this was an OAuth consent phishing attack alongside the fact that this did not raise any alarms on the logs. They stole an access token using this method of attack after completing MFA and used the token to being granted access past the Conditional Access Policies set in place to embed themselves deeper into the victim's portal.  

<img width="1600" height="900" alt="Week 2 Screenshot 1" src="https://github.com/user-attachments/assets/ce24733a-810b-401b-afbf-c2241ae4e8be" />
<img width="1600" height="900" alt="Week 2 Screenshot 2" src="https://github.com/user-attachments/assets/74ef309e-c026-49c4-a03c-b06827b95721" />
<img width="1600" height="900" alt="Week 2 Screeshot 3" src="https://github.com/user-attachments/assets/a8162f07-d377-41e8-b283-5d2244c29fa6" />
<img width="1600" height="900" alt="Week 2 Screenshot 4" src="https://github.com/user-attachments/assets/eeea281a-e177-428d-9535-77e0c87c9b83" />

Step 2 ESCALATE: This confirmed the suspicion that the victim was the owner of a legacy connector app. Further investigation led me to look in the Open Certificates & Secrets tab of this legacy app and check the Client secrets area of the page. After observing the activity from this clue I concluded that the attacker configured his way into using the victim’s power to make a brand new client secret on the app. In an attempt to gain more privileges, the attacker successfully used the new client to upgrade their authority over the apps directory permissions. In addition, they also established a sneaky way of remaining control by configuring the expiration date on the client secret to be online for an absurd amount of time. I concluded that this malicious agent was constantly looking for ways to penetrate into the portal further through granted themselves more privileged access, using weaker levels of assurance like client secrets and grounding their presence stay in power for an extended period of time. 

<img width="1600" height="900" alt="Week 2 Screenshot 5" src="https://github.com/user-attachments/assets/32b36747-04c4-4ba3-a508-062c5ced83da" />

Step 3 PIVOT: Trying to think outside of the box, I asked myself what I would do if I was an attacker trying to remain in control of my access to this compromised app registration. Since rotating secrets was a way for organizations to remain secure I decided to look in the Owner’s list of the legacy app to see if they created a new app registration since that would mean they could potentially have a dangerous amount of unregulated access by making new credentials at their discretion. After seeing there was a specific app registration present in the Owner’s list of the legacy app I went back to the app registration part of the Azure portal to find a service principal under the same name and investigated further. Then moving into the Branding & Properties section of under the manage section of the suspiciously created app registration was the internal notes that proved my conclusion. 

<img width="1600" height="900" alt="Week 2 Screenshot 7" src="https://github.com/user-attachments/assets/1b4c40b3-e0b3-43b9-ac20-71eb6aac6b8f" />


<img width="1600" height="900" alt="Week 2 Screenshot 8" src="https://github.com/user-attachments/assets/5737e1d2-f270-4c34-844a-134de2bf8eb8" />


 <img width="1600" height="900" alt="Week 2 Screenshot 9" src="https://github.com/user-attachments/assets/38f6114d-61bd-4935-85d2-0a286e09e4ea" />


<img width="1600" height="900" alt="Week 2 Screenshot 10" src="https://github.com/user-attachments/assets/d3a9cd83-10d2-46c1-8027-60e1e0372938" />

Step 4 PERSIST: This led me to my next move, which was looking for more ways that this agent could’ve integrated themselves deeper into it’s target. Like a stubborn tick on a dog that won’t come off, this attacker seemed to love putting multiple safeguards in place for them to remain in constant control. This led my investigation deeper into the Expose an API section of the legacy app because configuring a new API scope means having consistent way for them to ask the legacy app for access. 

<img width="1600" height="900" alt="Week 2 Screenshot 6" src="https://github.com/user-attachments/assets/37e7ab06-bb44-4760-96b8-b3fa00ac6782" />

<img width="1600" height="900" alt="Week 2 Screenshot 11" src="https://github.com/user-attachments/assets/d86f01da-be30-4983-a338-f445134565bb" />

Step 5 LOOT: Lastly, all roads led to the final construction of a hardy phishing URL that puts together the Expose an API sting from the last step, the redirect URI and the shady app client ID. This proves that users who grant access on a consent prompt gives the rogue app an authorization code that it can redeem for an access token for the specific API that has been exposed. This would give the rogue app the ability to use that token to behave according the scope’s permissions.

## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.


 be honest. Most people are surprised that any standard user can register an app by default, and that owning an app registration is effectively an unlogged privilege path that a review of Global Admins would completely miss.
 

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.



Findings and recommendations, written like a real report: revoke the client secret · remove the rogue service principal from Owners · delete the custom exposed API scope · revoke the OAuth2PermissionGrant explicitly, because containment does not remove it · remove the attacker redirect URI · review and reduce the Graph application permissions · disable default user app registration · audit every app registration's Owners list the same way you audit directory role membership · alert on new client secrets and new redirect URIs.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."
