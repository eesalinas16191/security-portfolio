# [Identity Breach of Suspicious Magnitude"]

## Scenario

In this scenario a malicious agent was granted access to an enterprise app through using a multi step OAuth consent-phishing attack which led to them stealing the necessary tokens necessary for them to bypass the authentication safeguards. They made several configurations inside the 2 app registrations for them to remain in control and reduce suspicion of their activity. This investigations answers the question of what the attacker was able to accomplish through clues left behind in the Azure portal. 



Reconstructed a five-stage OAuth consent-phishing kill chain in a live Azure tenant through forensic analysis of two linked app registrations."

## Environment

"Cloud computing, Cloud services, live multi-user Azure training tenant, Reader access."

## Investigation

These five steps listed below will outline how the investigation was orchestrated.

 ENTRY. A user was phished, completed MFA, and had the resulting session token stolen. That token carried an MFA-satisfied claim, so it sailed past Conditional Access. That user was also, through years of drift, still an Owner on a legacy connector app.

Step 1 ENTRY: I first investigated this breach from the source of it all. That source being a suspicious link being utilized to phish an employee who mistakenly and unknowingly granted permission to a malicious actor during this attack. Knowing this did not raise any alarms on the logs is what gave me the first clue of this being an OAuth consent phishing attack. They stole an access token using this method of attack after completing MFA and used the token to being granted access past the Conditional Access Policies set in place to embed themselves deeper into the victim's portal.  

<img width="1600" height="900" alt="Week 2 Screenshot 1" src="https://github.com/user-attachments/assets/ce24733a-810b-401b-afbf-c2241ae4e8be" />
<img width="1600" height="900" alt="Week 2 Screenshot 2" src="https://github.com/user-attachments/assets/74ef309e-c026-49c4-a03c-b06827b95721" />
<img width="1600" height="900" alt="Week 2 Screeshot 3" src="https://github.com/user-attachments/assets/a8162f07-d377-41e8-b283-5d2244c29fa6" />
<img width="1600" height="900" alt="Week 2 Screenshot 4" src="https://github.com/user-attachments/assets/eeea281a-e177-428d-9535-77e0c87c9b83" />


 ESCALATE. Using those Owner rights, the attacker minted a new client secret on the legacy app. That secret let them authenticate through the client credentials flow as the service principal itself, inheriting the app's directory permissions without ever signing in as a human again. Note the expiry date: set nearly a century out.

<img width="1600" height="900" alt="Week 2 Screenshot 5" src="https://github.com/user-attachments/assets/32b36747-04c4-4ba3-a508-062c5ced83da" />


 PIVOT. A single secret dies when it gets rotated. So the attacker registered their own app (every standard user can do this by default in Entra) and added its service principal to the legacy app's Owners list. Now they can re-credential the legacy app forever, even after the first secret is caught.

<img width="1600" height="900" alt="Week 2 Screenshot 7" src="https://github.com/user-attachments/assets/1b4c40b3-e0b3-43b9-ac20-71eb6aac6b8f" />


<img width="1600" height="900" alt="Week 2 Screenshot 8" src="https://github.com/user-attachments/assets/5737e1d2-f270-4c34-844a-134de2bf8eb8" />


 <img width="1600" height="900" alt="Week 2 Screenshot 9" src="https://github.com/user-attachments/assets/38f6114d-61bd-4935-85d2-0a286e09e4ea" />


<img width="1600" height="900" alt="Week 2 Screenshot 10" src="https://github.com/user-attachments/assets/d3a9cd83-10d2-46c1-8027-60e1e0372938" />


 PERSIST. Then the backup plan: a custom scope published on the legacy app's Expose an API blade. This turns the legacy app into a callable backend resource, which means the attacker's own app can request delegated access to it.

<img width="1600" height="900" alt="Week 2 Screenshot 6" src="https://github.com/user-attachments/assets/37e7ab06-bb44-4760-96b8-b3fa00ac6782" />

<img width="1600" height="900" alt="Week 2 Screenshot 11" src="https://github.com/user-attachments/assets/d86f01da-be30-4983-a338-f445134565bb" />


 LOOT. Finally, a redirect URI on the rogue app pointing at attacker-controlled infrastructure. Combining the rogue app's client ID, that redirect URI, and the exposed API scope produces a working phishing URL. A victim who is already signed in on a corporate device clicks Accept on a consent prompt, and the authorization code lands on the attacker's server.

## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.


 be honest. Most people are surprised that any standard user can register an app by default, and that owning an app registration is effectively an unlogged privilege path that a review of Global Admins would completely miss.
 

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.



Findings and recommendations, written like a real report: revoke the client secret · remove the rogue service principal from Owners · delete the custom exposed API scope · revoke the OAuth2PermissionGrant explicitly, because containment does not remove it · remove the attacker redirect URI · review and reduce the Graph application permissions · disable default user app registration · audit every app registration's Owners list the same way you audit directory role membership · alert on new client secrets and new redirect URIs.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."
