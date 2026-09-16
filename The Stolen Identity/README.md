# [The Stolen Identity"]

## Scenario

In this scenario a malicious agent was granted access to an enterprise app through using a multi step OAuth consent-phishing attack which led to them stealing the tokens necessary for them to bypass the authentication safeguards. They made several configurations inside the 2 app registrations for them to remain in control and reduce suspicion of their activity. This investigations answers the question of what the attacker was able to accomplish through clues left behind in the Azure portal. 

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

<img width="1600" height="900" alt="Week 2 Screenshot 11" src="https://github.com/user-attachments/assets/d86f01da-be30-4983-a338-f445134565bb" />

Step 5 LOOT: Lastly, all roads led to the final construction of a hardy phishing URL that puts together the Expose an API sting from the last step, the redirect URI and the shady app client ID. This proves that users who grant access on a consent prompt gives the rogue app an authorization code that it can redeem for an access token for the specific API that has been exposed. This would give the rogue app the ability to use that token to behave according the scope’s permissions.

<img width="1600" height="900" alt="Week 2 Screenshot 13" src="https://github.com/user-attachments/assets/aad4a552-9fec-4ef8-9ed3-02fcf7c7933c" />


## What broke / what surprised me

What didn't break? I ask myself this after looking back on the investigation because during every step something was always off or out of place. Everything from the Expose an API section of the legacy app being deceptively manipulated and the attacker creating a rogue app registration connected to a specific service principal. To the attacker creating a backend path through the legacy app registration and using that method to collect tokens. All of these actions were done in way that abused and tricked specific tools to misuse their authority in malicious ways. This led to me being surprised at how cunning a malicious agent can be if they have true grit. At every step of the investigation I was surprised at how much precaution this attacker had to think outside of the box and create multiple buffers so that they could stay in control. It really opened my eyes at to how crafty some attacks can be and made me realize that constantly learning about these attacks is great way to be aware and stay safe.
 
## Findings and recommendations

I determined that identity is extremely important to place safeguards around in order to prevent intrusions. My recommendations would be to have users be very cautious when faced with the opportunity to give permission to apps in general even if they look trustworthy. Alongside with having active investigations targeting suspicious app in the Owner's list of app registrations. I believe another way to prevent this happening again would be to configure certain policies in place that can flag suspicious activity regarding any changes to new client secrets. Another recommendation would be to use a certificate or a managed identity for more secure authentication methods instead of client secrets. Getting rid of the service principle tied to the rogue app registration alongside the manipulated API scope that the attacker created should be put into practice as well. In addition, anything related to the attacker's actions like their redirect URI should also be deleted. Default users should also be placed on a stricter set of privileges by getting rid of their ability to create app registrations. Taking a look at the Graph app permissions should also be of concern as reducing the permissions can help reduce the range of an attack.   

<img width="1600" height="900" alt="Week 2 Screenshot 6" src="https://github.com/user-attachments/assets/70aba9e3-23f6-4e3a-b166-e82090dc1de5" />

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."
I learned that OAuth stood for Open Authorization and not Open Authentication because there is a distinction between the 2 terms. Since authentication is for proving identity, authorization deals with granting permission to other apps as to not have to create new accounts. This made more sense regarding the investigation having to deal with this type of attack. In my opinion, I learned that OAuth is more of an appeal to convenience over security even if at it's core is still rooted in security. In the end, I've learned that I would've implemented a stricter set of policies in place for many of the tools in the portal. 
