# 30-Days-Identity-Access-Management-in-conjunction-with-ISO27001-
I'll be sharing my thoughts on IAM for 30 days which comes with deep and analytical insights with additional Cloud IAM hands-on


DAY 1 — Introduction to IAM
IAM stands for Identity and Access Management. It is the discipline of making sure the right people (and systems) can access the right resources at the right times,and nothing more. 
The four pillars are: 
i)Identity (who you are)
ii)Access (what you can touch) iii)Authentication (proving who you are) iv)Authorization (what you're allowed to do). 
Without IAM, every user is an administrator, and every breach is a catastrophe.
Highlight: IAM is not a product — it is a security philosophy . Every cloud provider, every application, and every OS implements these same four pillars differently.

DAY 2 : Authentication Basics 
- Authentication is the act of proving your identity. It relies on three factor categories: 
i )Something you know (password, PIN)
ii) Something you have (hardware key, smart card)
iii) Something you are (fingerprint, face, retina)
- Single-factor (password only) is weak. 
- Multi-factor authentication (MFA) combines two or more factors and is the single most effective control against credential theft.
Highlight: A password is not authentication. It is one factor  of authentication. Strong authentication requires diversity across the three categories.

DAY 3  : Authorization Basics
- Authorization happens after authentication. It answers: "Now that I know who you are, what are you allowed to do?"  
- Two key distinctions: Permissions are the granular rights (e.g.,  s3:GetObject ), while Privileges are the elevated level of access (e.g., root/admin). 
- The Principle of Least Privilege (PoLP) states that every user, process, and system should have the minimum  access necessary to perform its function, and no more.
Highlight: Never confuse "can access" with "should access." Authorization is where most breaches originate through over-permissioned accounts.

DAY 4 : IAM Components
- IAM systems are built from five core components: 
a. Users (individual identities)
b. Groups (collections of users with shared permissions)
c. Roles (temporary identities assumed by users or services)
d. Policies (documents that define permissions)
e. Resources (the assets being protected e.g S3 buckets, EC2 instances, databases). 
- Understanding how these five interact is the foundation of every IAM decision you will ever make.
Highlight: Users and Roles both represent identities, but Roles are assumed , not logged into directly. Services (like an EC2 instance or Lambda function) use Roles, not Users.

DAY 5 of 30 Days Identity and Access Management Challenge : Access Control Models
- There are four classic models: 
a. DAC (Discretionary Access Control, owners decide who accesses their resources, like file sharing)
b. MAC (Mandatory Access Control, the system enforces strict labels, like military classifications)
c. RBAC (Role-Based Access Control, permissions are tied to job roles, like "Developer" or "Auditor")
d. ABAC (Attribute-Based Access Control, permissions are granted based on dynamic attributes like department, location, or time of day). 
- Modern cloud IAM (especially AWS) is primarily RBAC with ABAC extensions.
Highlight: RBAC scales organizations. ABAC scales complexity. Most enterprises start with RBAC and layer ABAC as they mature.

DAY 6 : IAM in the Real World
- IAM is everywhere: your phone uses biometrics (authentication), your email uses MFA, your company's VPN uses RBAC, and cloud platforms use policy-driven authorization. 
- Common IAM challenges include:
 a. Privilege creep (accumulating access over time)
b. Orphaned accounts (users who left but accounts remain)
c. Shadow IT (unauthorized cloud apps)
d. Overly permissive service accounts. 
- In healthcare, a single misconfigured IAM policy can expose thousands of patient records.
Key Takeaway: IAM failures are rarely technical, they are operational . The technology works; the governance fails.

Days 8-12 of 30 days IAM Challenge : Identity Access & Management 
--> In one of the hands-on, I applied the Principle of Least Privilege (PoLP) by creating users (dev - 01 & dev - 02) in my AWS account.  I attached the policy of "Read-only access " to dev-01. I added both dev-01 & dev-02 to developers group. To verify the PoLP, I logged in as dev-01 IAM user and tried to create an S3 bucket inside the AWS main account, but it was rejected because the AWS main account restricted dev-01 IAM user to just "Read-only access".
→ Users & Groups : attached policies to containers, not individuals. Scales cleaner.
→ Roles & Trust Policies : identities without passwords. Services assume them. 
→ Custom JSON Policies : granular S3 access down to one bucket. Explicit Actions. Explicit Resources. One "*" in the wrong place = global admin access. 
Then I hit the governance layer:
→ Permission Boundaries : guardrails that override even admin policies. They set the ceiling, not the floor. 
→ IAM Policy Simulator :test every action before touching production. Debugger for policy logic.
Highlight : Boundaries are your safety net. The simulator is your crystal ball. Both are non-negotiable for any environment that matters.
---> Least Privilege, Separation of Duties, Just-in-Time Access :
No one person should have the sole rights to spear head all phases. Standing admin rights are a liability. The future is zero standing privileges.
Real policies. Real enforcement. Real audit mindset.

DAY 13 —  14  of 30 days Identity and Access Management Challenge :
Policies (Hands-on)& Multi-Factor Authentication (MFA)
- Built a custom IAM policy granting granular S3 access to a single bucket ( johannes22bucket ) using  s3:GetObject  and  s3:ListBucket . The IAM user with the attached policy could neither create or access another except johannes22, neither could it perform any other action within the main account due to the restriction.
- I Discovered that the S3 console requires  s3:ListAllMyBuckets  just to display the bucket list, without it, an authorized bucket is invisible in the UI. Also saw the IAM dashboard throw "Access Denied" errors because the test user lacks  iam:ListMFADevices  and  iam:GetAccountSummary  permissions.
- Key Lesson: Least privilege works. The console hiding resources and dashboard elements isn't a bug — it's proof the policy is enforcing boundaries.
-  In audits, finding users with  AdministratorAccess  "to avoid console errors" is a critical finding. The correct state is exactly what you built today: users navigate directly to their authorized services, and denied actions are logged evidence of good governance.
- MFA requires two or more authentication factors. Types include: TOTP (Time-based One-Time Password ,Google Authenticator, Authy), Hardware tokens (YubiKey), SMS (weaker, susceptible to SIM swapping), and Push notifications (convenient but vulnerable to MFA fatigue attacks).
-  In AWS, MFA can protect console sign-in, API calls, and sensitive operations like deleting S3 buckets.
Key takeaway: MFA is not optional for root accounts or privileged users. It is the difference between a phishing incident and a breach.

Day 15-17 of 30 days IAM Challenge :Single Sign-On, Federation & Directory Services
•I tested permission levels on a User(boundary tester)that was given permission to create extra users due to the "Create User" policy that I attached to it.
• I applied permission  boundary to same User (boundary tester) by implementing a "DevBoundary" policy to it so as to ensure it doesn't attach policies. Now, this user can create extra users, but does not have the rights to attach policies to them
• Permission boundary restricts a user to a certain point despite whatever permission they have.SSO allows a user to log in once and gain access to multiple applications without re-authenticating. It works through identity federation, your identity provider (IdP) authenticates you, then passes a token (SAML, OIDC) to each service, which trusts the IdP. 
• Single Sign-On is not just convenience, it is a security control. When an employee leaves, you disable one account, and their access to Slack, AWS, GitHub, and email dies simultaneously.
• Federation is the trust relationship between an Identity Provider (IdP like Okta, Azure AD, or AWS IAM Identity Center) and a Service Provider (like AWS). The user authenticates to the IdP, receives a signed token (SAML assertion or JWT), and presents it to AWS.
• Directory is a database of users, groups, and organizational units, the most common standard is LDAP (Lightweight Directory Access Protocol). In the cloud, Azure Active Directory (now Entra ID) and AWS Directory Service  provide centralized identity, e.g  Only users in the Finance department can access this S3 bucket.


