# API Security: The Core Threats Every Developer Must Confront

APIs have become the connective tissue of modern software. From banking and healthcare to retail and autonomous systems, APIs expose application logic and sensitive data such as personally identifiable information (PII). And because of that, they have increasingly become a prime target for attackers. The numbers bear this out: a staggering 57% of organizations have suffered an API-related data breach in the past two years, with 73% of those experiencing three or more incidents.

What makes this problem particularly acute in 2026 is the rise of agentic AI. APIs are no longer primarily exercised by humans clicking through applications—they are increasingly operated by agentic workflows, autonomous systems that discover endpoints, chain API calls, and make decisions at machine speed. Authorization gaps, unsafe integrations, and inventory blind spots that once required deliberate, manual effort can now be uncovered and exploited continuously.

This article walks through the most critical API security risks developers face today, grounded in the OWASP API Security Top 10—the industry's authoritative framework—and supplemented with real-world examples and practical mitigation strategies.

---

## The OWASP API Security Top 10: A Developer's Guide

The OWASP API Security Top 10, first published in 2019 and updated in 2023, is a forward-looking awareness document for a fast-paced industry. The 2023 edition reflects four years of evolution in the API security landscape: API traffic increased at a fast pace, new protocols gained traction, attackers developed new techniques, and many new security solutions emerged. The fundamental failure modes, however, remain consistent.

### API1:2023 – Broken Object Level Authorization (BOLA)

BOLA is the most critical API security risk, and for good reason. APIs tend to expose endpoints that handle object identifiers—user IDs, account numbers, order IDs—creating a wide attack surface for object-level access control issues. The problem is simple but devastating: the API receives an object ID from the client and fails to verify whether the authenticated user has permission to access that object.

In 2025, BOLA was the second most frequently reported API vulnerability. One particularly stark example involved Growatt solar inverters used in industrial and commercial systems. A BOLA flaw in the API allowed hackers to take control of solar-powered IoT devices inside users' homes simply by manipulating a device ID parameter—a textbook case of failing to verify whether the current user was authorized for that device.

**The fix:** Object-level authorization checks must be considered in every function that accesses a data source using an ID from the user. Never trust only the ID sent in the URL; filter database queries by the resource owner. Authorization checks for individual resources are usually too fine-grained to offload to centralized platforms like API gateways—the responsibility sits with API developers to implement proper checks at the endpoint.

### API2:2023 – Broken Authentication

Authentication mechanisms are often implemented incorrectly, allowing attackers to compromise authentication tokens or exploit implementation flaws to assume other users' identities. When a system's ability to identify the client is compromised, API security as a whole is compromised.

According to APISecurity.io's analysis of 2025 incidents, Missing Authentication was the *most frequent* API vulnerability of the year, accounting for 17% of all API incidents covered. In one case, patient data in the UK healthcare system was reportedly exposed through an unauthenticated API. At Intel, a `getAccessToken` API endpoint would return valid tokens without requiring any authentication whatsoever.

**The fix:** Use modern, standards-based authentication. OAuth 2.0 is considered the gold standard for REST API authentication and authorization. OpenID Connect (OIDC) adds an identity layer for user-based access. For tokens, use short-lived access tokens with refresh tokens, store them securely, and rotate secrets regularly. And critically: enforce authentication for *every* sensitive operation—not just the ones you remember to check.

### API3:2023 – Broken Object Property Level Authorization (BOPLA)

This category combines two previously separate risks—Excessive Data Exposure and Mass Assignment—and focuses on their common root cause: improper authorization validation at the object property level.

Excessive data exposure occurs when an API returns more data than the client needs, potentially exposing sensitive fields. Mass assignment happens when an API automatically binds client-supplied data to internal objects without proper filtering, allowing attackers to modify fields they shouldn't have access to (like setting `isAdmin` to `true`).

**The fix:** Implement explicit response shaping by role—return only what's necessary. Use allow-listed write models rather than implicit binding or mass assignment. In agentic environments, this becomes especially critical because agents excel at discovering writable and readable fields through mutation and response comparison—they can automatically test hundreds of field combinations.

### API4:2023 – Unrestricted Resource Consumption

Satisfying API requests requires resources: network bandwidth, CPU, memory, and storage. Other resources like email, SMS, phone calls, and biometrics validation are made available via API integrations and are paid for per request. Without limits, attackers can cause denial of service or drive up operational costs.

When agents operate at machine speed, resource abuse is no longer accidental—APIs that lack limits can be consumed continuously, triggering denial of service or unexpected cost explosions.

**The fix:** Implement rate limits, quotas, payload constraints, and timeouts. Combine different limit scopes for layered protection: per-IP, per-account, per-API-key, and per-endpoint. Monitor for anomalous usage patterns that indicate automated abuse.

### API5:2023 – Broken Function Level Authorization (BFLA)

Complex access control policies with different hierarchies, groups, and roles—combined with unclear separation between administrative and regular functions—tend to lead to authorization flaws. Attackers can exploit these issues to gain access to other users' resources or administrative functions.

In agentic workflows, this becomes trivial to exploit. Agents can enumerate and invoke privileged functions once routing patterns are learned—hidden or undocumented admin paths are especially vulnerable.

**The fix:** Adopt a deny-by-default authorization policy on every endpoint. Automate testing that verifies role enforcement across all functions, not just documented ones.

### API6:2023 – Unrestricted Access to Sensitive Business Flows

APIs vulnerable to this risk expose business flows—such as buying a ticket, posting a comment, or processing a password reset—without compensating for how the functionality could harm the business if used excessively in an automated manner. This doesn't necessarily come from implementation bugs; it's about business logic.

This category becomes significantly more impactful in an agentic world. Business flows like purchases, promotions, onboarding, and password resets are ideal targets for automation and abuse.

**The fix:** Implement flow-level protections: velocity controls, step-up authentication, and anomaly detection. Test business logic abuse scenarios, not just technical failures.

### API7:2023 – Server-Side Request Forgery (SSRF)

SSRF flaws occur when an API fetches a remote resource without validating the user-supplied URI. This enables an attacker to coerce the application to send a crafted request to an unexpected destination, even when protected by a firewall or VPN.

**The fix:** Validate all user-supplied URLs and paths before your API uses them. Implement allow-lists for destinations, restrict which protocols are permitted, and never return raw response data from internal services to the client.

### API8:2023 – Security Misconfiguration

APIs and the systems supporting them typically contain complex configurations, and security misconfigurations are pervasive. Default passwords, debug mode left enabled, unnecessary HTTP methods, and exposed error details are all common examples.

**The fix:** Harden configurations systematically. Disable debug mode in production. Remove unnecessary HTTP methods. Implement automated configuration scanning as part of your CI/CD pipeline. Use infrastructure-as-code to version and audit configurations.

### API9:2023 – Improper Inventory Management

This risk addresses something critical but often overlooked: you cannot secure what you don't know exists. Unseen or unknown APIs—often called "shadow" or "zombie" APIs—represent the largest attack vector in most organizations.

**The fix:** Maintain an active inventory of all API endpoints via OpenAPI/Swagger specifications. Enable API discovery to identify undocumented APIs. Include APIs in regular security checks and penetration tests.

### API10:2023 – Unsafe Consumption of APIs

Developers tend to trust data received from third-party APIs more than user input, and so tend to adopt weaker security standards. Attackers have started targeting integrated third-party services instead of hitting the target API directly.

**The fix:** Apply the same security checks and verification steps to third-party API responses as you would to any unknown client input. Validate response parameters, establish allowable redirect URLs, and limit resource allocation.

---

## Foundational Best Practices: The Non-Negotiables

Beyond the OWASP Top 10, several foundational practices form the bedrock of API security.

### Encrypt Everything with TLS

One of the most basic but essential API security practices is ensuring all communication occurs over HTTPS with TLS. TLS prevents data from being transmitted in plaintext, protecting against man-in-the-middle attacks. Use TLS 1.2 as a minimum—TLS 1.3 is preferred. Implement HSTS headers to enforce secure connections and prevent downgrade attacks.

For sensitive internal API-to-API communications, consider mutual TLS (mTLS), which allows both sides to authenticate each other, providing an extra layer of protection in zero-trust architectures.

### Validate Every Input, Always

Effective input validation is essential for preventing injection attacks, data tampering, and denial-of-service attacks. Validate input as early as possible—ideally when data is received from external sources—and ensure validation at every system layer. Every input must be treated as untrusted until verified.

### Implement Rate Limiting

Rate limiting restricts the number of requests a client can make within a defined period. It prevents abuse, mitigates denial-of-service attacks, and ensures fair usage of resources. Configure rate limits per client using unique identifiers like API keys, access tokens, or IP addresses.

### Adopt an API Gateway

An API gateway acts as a security control hub, centralizing authentication, authorization, rate limiting, and logging. It protects APIs from common web vulnerabilities like SQL and JSON injection attacks and helps prevent denial-of-service attacks.

### Shift Security Left

Security must be integrated early in the development lifecycle—not bolted on at the end. NIST's Guidelines for API Protection for Cloud-Native Systems recommends identifying risk factors and vulnerabilities during various activities of API development and runtime, and implementing controls that span the entire API lifecycle.

---


API security isn't about implementing a single silver-bullet solution—it's about building a layered defense that spans authentication, authorization, encryption, validation, and monitoring. The OWASP API Security Top 10 provides a practical framework for understanding where failures occur and which controls matter most.

In 2026, with agentic AI systems amplifying every weakness, the stakes are higher than ever. Authorization gaps that once required deliberate, manual effort can now be discovered and exploited continuously. Keeping track of your API endpoints and continually verifying authentication and authorization controls—both pre-production and in production—is no longer optional.

Start with the basics: enforce authentication on every endpoint, implement object-level authorization checks, validate all input, encrypt all traffic, and maintain an inventory of every API you expose. From there, build outward. The threats are real, the attackers are sophisticated, and the cost of getting it wrong has never been higher.
