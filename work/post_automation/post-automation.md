Here are the detailed, up-to-date (as of February 2026) steps to obtain API keys / credentials for every platform in your table.  
I’ve included official links, exact navigation, what credentials you get, and any important caveats (e.g., approvals, restrictions).1. LinkedIn – UGC Posts v2 (3,000 char limit)Credentials needed: Client ID (API Key) + Client Secret + OAuth 2.0 Access Token (with UGC permissions).  
Note: UGC Posts is a private permission. Basic app creation is open, but full UGC write access is granted only to approved developers.

1. Go to the LinkedIn Developer Portal: [https://www.linkedin.com/developers/apps](https://www.linkedin.com/developers/apps)
2. Sign in with your LinkedIn account (must be a personal profile with a verified email).
3. Click Create app (top right).
4. Fill in:
    - App name
    - LinkedIn Page (optional but recommended)
    - App logo
    - Company / description
    - Privacy policy URL (can be a placeholder)
    - Terms of service URL
5. Check the legal agreement box → Create app.
6. Go to the Auth tab → copy Client ID (this is your API Key) and Client Secret.
7. Under Products → request Share on LinkedIn or Marketing Developer Platform (UGC Posts falls under this).
8. For production UGC posting, submit a request via the “Request access” form in the app dashboard (LinkedIn reviews it).
9. Once approved, add the scope w_member_social (or r_ugc_post for read) in your OAuth flow.
10. Generate an Access Token using OAuth 2.0 (use the standard LinkedIn OAuth endpoint).

Official docs:

- UGC Post API → [https://learn.microsoft.com/en-us/linkedin/compliance/integrations/shares/ugc-post-api](https://learn.microsoft.com/en-us/linkedin/compliance/integrations/shares/ugc-post-api)
- My Apps → [https://www.linkedin.com/secure/developer](https://www.linkedin.com/secure/developer)

2. X.com (Twitter) – 280 char limit (OAuth 1.0a + Tweets v2)Credentials needed: API Key + API Secret + Access Token + Access Secret (OAuth 1.0a User Context) or Bearer Token.

3. Go to [https://developer.x.com](https://developer.x.com)
4. Sign in with your X account.
5. Click Sign up for a developer account (if you don’t have one) → answer the brief questions (what you’ll build, etc.).
6. Once approved (usually instant), go to Developer Portal → Projects & Apps → Create Project.
7. Name the project and app.
8. In the app settings, under User authentication settings → Edit → enable OAuth 1.0a and set Read + Write + Direct Messages (for posting).
9. Set callback URL (for web apps) or leave blank for script/app-only.
10. Go to Keys and tokens tab:
    - API Key and API Secret (regeneratable)
    - Access Token and Access Secret (for your own account)
    - Bearer Token (for read-only)

Official guide: [https://docs.x.com/fundamentals/authentication/oauth-1-0a/api-key-and-secret](https://docs.x.com/fundamentals/authentication/oauth-1-0a/api-key-and-secret)3. Threads – 500 char limit (Threads Graph API)Credentials needed: Threads App ID + App Secret + User Access Token (with threads_content_publish).

1. Go to Meta for Developers: [https://developers.facebook.com/apps](https://developers.facebook.com/apps)
2. Click Create App → choose Other or directly Threads use case.
3. Select Business or Consumer type (Consumer works for personal posting).
4. Fill app name, contact email → Create app.
5. In the left menu, add the Threads product.
6. Note your Threads App ID and App Secret (under App Settings → Basic).
7. To post on your own account:
    - Use the Authorization Window URL (replace with your values):  
        https://threads.net/oauth/authorize?client_id=YOUR_APP_ID&redirect_uri=YOUR_REDIRECT_URI&scope=threads_basic,threads_content_publish&response_type=code
    - Authorize → you’ll get a code.
    - Exchange the code for a short-lived token:  
        POST https://graph.threads.net/oauth/access_token with client_id, client_secret, code, grant_type=authorization_code.
    - Exchange short-lived for long-lived token (valid 60 days).
8. Use the long-lived user access token in every API call.

Official get-started: [https://developers.facebook.com/docs/threads/get-started/](https://developers.facebook.com/docs/threads/get-started/)4. Reddit – 40,000 char limit (Reddit OAuth + Submit)Credentials needed: Client ID + Client Secret (OAuth2).

1. Log in to Reddit on desktop.
2. Go to [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps)
3. ![](work/post_automation/img/post-automation-20260224231430507.png)
4. Click create an app (or “create another app”).
5. ![](work/post_automation/img/post-automation-20260224231501296.png)
6. Choose script (for personal bots) or web app.
7. Fill:
    - Name
    - Description
    - About URL (optional)
    - Redirect URI → for script use http://localhost:8080
8. Click create app.
![](work/post_automation/img/post-automation-20260224231639932.png)
9. The Client ID is the random string under the app name.
10. Click edit → the Client Secret appears in the green box.

Keep these two safe — they never expire unless you revoke the app.5. Medium – 100,000 char limit (Medium Integration API)Important restriction (as of 2026): Medium is no longer issuing new integration tokens. All existing tokens continue to work, but new ones cannot be created.If you already have an old token:

1. Go to [https://medium.com/me/settings](https://medium.com/me/settings)
2. Scroll to Integration tokens → your existing tokens are listed.

If you don’t have one:

- You cannot get a new token anymore.
- Alternatives: Use Medium’s mobile apps, import via URL, or IFTTT/RSS.

Official statement: [https://help.medium.com/hc/en-us/articles/213480228-API-Importing](https://help.medium.com/hc/en-us/articles/213480228-API-Importing)6. Dev.to – 100,000 char limit (Dev.to API)Credentials needed: Simple API Key (no OAuth).

1. Log in to [https://dev.to](https://dev.to)
2. Click your avatar → Settings.
3. Go to Extensions (or Account in newer UI).
4. Scroll to DEV Community API Keys section.
5. Enter a description (e.g., “My cross-poster”).
6. Click Generate API Key.
7. Copy the key immediately — it is shown only once.

Docs: [https://developers.dev.to](https://developers.dev.to) (all authenticated endpoints accept api-key: YOUR_KEY header).7. Hashnode – 100,000 char limit (Hashnode GraphQL)Credentials needed: Personal Access Token (Bearer).

1. Log in to [https://hashnode.com](https://hashnode.com)
2. Click your avatar → Settings.
3. In the left sidebar, click Developer (or API & Integrations).
4. Under Personal Access Tokens, click Generate new token.
5. Give it a name and select scopes (at minimum post:publish or post:create).
6. Click Generate → copy the token (shown only once).

Use it as:  
Authorization: Bearer YOUR_TOKEN  
in GraphQL requests to https://gql.hashnode.com

---

Quick reference table of what you actually copy-paste:

|Platform|Main Credentials|Where to store safely|
|---|---|---|
|LinkedIn|Client ID + Client Secret|.env file|
|X|API Key + API Secret + Access Token|.env file|
|Threads|App ID + App Secret + User Token|.env file|
|Reddit|Client ID + Client Secret|.env file|
|Medium|Integration Token (if you have one)|.env file|
|Dev.to|API Key|.env file|
|Hashnode|Personal Access Token|.env file|

Security tip: Never commit these to GitHub. Use environment variables or a secret manager.If you hit any approval step (LinkedIn UGC, X rate limits, etc.) or need sample OAuth code in your language, just let me know which platform and I’ll give you the exact next steps!