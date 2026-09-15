# [Identity Breach of Suspicious Magnitude"]

## Scenario

In this scenario a malicious agent was granted access to a privileged Microsoft account through stealing private credentials necessary for them to bypass the authentication safeguards. The malicious agent then made the configurations necessary for them to remain in control and reduce suspicion of their activity. This investigations answers the question of what the attacker was able to do through clues left behind in the portal. 



Reconstructed a five-stage OAuth consent-phishing kill chain in a live Azure tenant through forensic analysis of two linked app registrations."

## Environment

"Cloud computing, Cloud services, live multi-user Azure training tenant, Reader access."

## Investigation
The core. Numbered steps IN YOUR OWN WORDS: what you looked at, what you found, what you concluded at each step. 6 to 12 screenshots of meaningful moments (portal views, query results, before/after).

1. ENTRY. A user was phished, completed MFA, and had the resulting session token stolen. That token carried an MFA-satisfied claim, so it sailed past Conditional Access. That user was also, through years of drift, still an Owner on a legacy connector app.



2. ESCALATE. Using those Owner rights, the attacker minted a new client secret on the legacy app. That secret let them authenticate through the client credentials flow as the service principal itself, inheriting the app's directory permissions without ever signing in as a human again. Note the expiry date: set nearly a century out.



3. PIVOT. A single secret dies when it gets rotated. So the attacker registered their own app (every standard user can do this by default in Entra) and added its service principal to the legacy app's Owners list. Now they can re-credential the legacy app forever, even after the first secret is caught.



4. PERSIST. Then the backup plan: a custom scope published on the legacy app's Expose an API blade. This turns the legacy app into a callable backend resource, which means the attacker's own app can request delegated access to it.



5. LOOT. Finally, a redirect URI on the rogue app pointing at attacker-controlled infrastructure. Combining the rogue app's client ID, that redirect URI, and the exposed API scope produces a working phishing URL. A victim who is already signed in on a corporate device clicks Accept on a consent prompt, and the authorization code lands on the attacker's server.

## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.


 be honest. Most people are surprised that any standard user can register an app by default, and that owning an app registration is effectively an unlogged privilege path that a review of Global Admins would completely miss.
 

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.



Findings and recommendations, written like a real report: revoke the client secret · remove the rogue service principal from Owners · delete the custom exposed API scope · revoke the OAuth2PermissionGrant explicitly, because containment does not remove it · remove the attacker redirect URI · review and reduce the Graph application permissions · disable default user app registration · audit every app registration's Owners list the same way you audit directory role membership · alert on new client secrets and new redirect URIs.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."
