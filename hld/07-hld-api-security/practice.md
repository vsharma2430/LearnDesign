# Practice Questions and Answers

Use these questions to test whether you can apply API security concepts to concrete requests, credentials, and failure cases. Strong answers state the trust boundary and explain what the service itself must validate.

## 1) What is the difference between authentication and authorization?

**Answer:**

Authentication establishes who or what is making the request. Authorization decides whether that identity may perform a particular action on a particular resource. A valid login does not imply access to every user record or operation. The API should validate the credential, derive identity from trusted claims, and perform resource-level authorization in the service that owns the data.

## 2) Why must a service authorize a request if the API gateway already authenticated it?

**Answer:**

The gateway may verify identity and enforce coarse route policy, but it usually does not own the service's complete business and resource context. The service must verify that the caller can perform the requested action on the specific object and tenant. Otherwise, a caller may change an object ID and access another user's data, or invoke an operation that the gateway cannot distinguish.

## 3) When would you choose a server-side session instead of a self-contained JWT?

**Answer:**

Sessions are attractive when immediate revocation, centralized state, or browser-oriented login is important. A session identifier is opaque and can be invalidated in the session store. A self-contained JWT can be validated locally with low per-request lookup cost, but revoking it before expiry requires additional state or short lifetimes. Sessions require a highly available shared store at scale; JWTs require safe key distribution, rotation, and claim validation.

## 4) Does a signed JWT hide its claims?

**Answer:**

No. A normal signed JWT is encoded, not encrypted. Anyone holding it can decode and read the payload. The signature protects integrity and helps authenticate the issuer when verified correctly; it does not provide confidentiality. Do not put secrets or sensitive personal data in a signed token.

## 5) What must an API validate when it receives a JWT?

**Answer:**

It should pin permitted algorithms, verify the signature against a trusted issuer key, validate issuer and audience, enforce expiry and not-before times with only justified clock skew, and check required scopes or claims. It should reject unknown key IDs, malformed tokens, and inappropriate token types. A valid signature alone does not mean the token is intended for this API or that its subject is allowed to access a resource.

## 6) How is OAuth 2.0 different from OpenID Connect?

**Answer:**

OAuth 2.0 is an authorization framework for delegated access to APIs. OpenID Connect adds an authentication and identity layer over OAuth 2.0, including an ID token and standardized identity claims. An access token is meant for a resource server; an ID token tells the client about an authentication event. They should not be substituted for each other.

## 7) Which OAuth flow should a native or browser application use for user sign-in?

**Answer:**

Use Authorization Code with PKCE. PKCE binds the authorization-code exchange to a verifier held by the client, reducing the value of an intercepted code. Use exact registered redirect URIs, `state` to correlate the browser response, and `nonce` when validating an OIDC ID token. Public clients must not rely on a client secret embedded in the application because users can extract it.

## 8) How do SSO and OIDC relate?

**Answer:**

SSO is the user experience and system capability of signing in once and accessing multiple applications. OIDC is one protocol that can implement SSO; SAML is another common enterprise protocol. Each relying application must still validate issuer, audience, signature, expiry, and request correlation. SSO does not automatically provision accounts, revoke every local session, or authorize access to each application's resources.

## 9) When are API keys appropriate, and what are their limitations?

**Answer:**

API keys can identify an integration for metering, quotas, and relatively simple access to low-risk APIs. They are bearer secrets, so leakage can allow use by anyone who obtains one. They do not prove which end user is making a request and are not a replacement for scoped user authorization. Keys need ownership, least privilege, expiry or rotation, revocation, secure storage, and redaction from logs. A key embedded in a public mobile or browser app cannot be kept secret.

## 10) Compare API keys, OAuth access tokens, and mTLS for service identity.

**Answer:**

| Mechanism | What it establishes well | Main limitation |
|---|---|---|
| API key | Integration/project identity and metering | Bearer secret; often coarse access and difficult end-user attribution |
| OAuth access token | Issuer-verified identity with audience and scopes | Requires token issuer and correct claims validation |
| mTLS | Possession of a trusted workload certificate at the transport boundary | Requires certificate lifecycle and does not by itself express all application permissions |

The choice depends on workload platform, required authorization granularity, latency, and credential operations. Strong systems may combine workload identity with scoped authorization rather than relying on one mechanism for every security need.

## 11) What counts as multi-factor authentication?

**Answer:**

MFA uses at least two independent categories: knowledge (something known), possession (something held), and inherence (something the user is). Two passwords are not two factors. Passkeys or security keys are phishing-resistant; TOTP is broadly supported but can be phished; SMS is a weaker fallback because phone numbers can be taken over. Account recovery must be protected as carefully as sign-in.

## 12) How do you secure service-to-service calls?

**Answer:**

Give each workload a distinct identity, use TLS (often mTLS) or short-lived issuer-signed workload tokens, scope permissions to the target service, and automate credential rotation. Validate audience and issuer, avoid fleet-wide shared secrets, and distinguish the calling service from any end user whose request it is carrying. The receiving service should authorize the requested operation rather than trusting network location alone.

## 13) What happens if a signing key is compromised?

**Answer:**

Stop issuing with the compromised key, remove or distrust it in verifiers according to incident procedure, rotate to a new key, and assess tokens issued during the exposure. Because removing a key may invalidate many legitimate tokens, the response needs a balance between containment and availability. Short token lifetimes, key IDs, automated JWKS refresh, overlapping normal rotation, and an emergency revocation path reduce impact.

## 14) How should a browser application protect session cookies?

**Answer:**

Use `Secure` so cookies travel only over HTTPS, `HttpOnly` to reduce JavaScript access, and an appropriate `SameSite` policy. Cookie-based authentication needs CSRF defenses when the browser attaches cookies automatically. Also use short and risk-appropriate session lifetimes, rotate the session identifier after authentication or privilege changes, and invalidate sessions at logout and account recovery.

## 15) What is the difference between a 401 and a 403 response?

**Answer:**

401 indicates that valid authentication credentials are missing or could not be accepted; the client may need to authenticate. 403 indicates that the caller is authenticated but lacks permission. Some systems return 404 instead of 403 when revealing whether a sensitive resource exists would itself leak information. The API should be consistent and avoid exposing details that help enumerate accounts or resources.

## Quick revision checklist

- Separate identity verification from object- and action-level authorization.
- Know what sessions, JWTs, OAuth access tokens, and OIDC ID tokens are for.
- Validate the audience and issuer, not only a token signature.
- Use Authorization Code with PKCE for interactive public clients.
- Treat API keys and bearer tokens as secrets and design their lifecycle.
- Prefer phishing-resistant MFA for high-risk accounts and secure recovery paths.
- Use distinct, short-lived, least-privilege identities between services.

