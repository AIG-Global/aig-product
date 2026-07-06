# Core Services Specification Template

**Standard template for all AIG platform services**

Every service specification follows this structure to ensure consistency, completeness, and clarity.

---

## Service Specification Template

### 1. Service Identity

```yaml
Service Name: [Name of service]
Service Code: [e.g., srv-identity, srv-ai-router]
Team Ownership: [Owner team name]
Status: [Design | Development | Beta | Production]
Version: [Current version, e.g., 1.0.0]
Last Updated: [Date]
```

### 2. Purpose & Principles

**One-line purpose:**
[Concise explanation of why this service exists]

**Responsibilities (What this service owns):**
- [Specific responsibility]
- [Specific responsibility]
- [Not responsible for: X (owned by other service)]

**Principles:**
- [Core principle]
- [Core principle]

### 3. Functional Requirements

**Primary Use Cases:**
1. [Use case 1 with actor and outcome]
2. [Use case 2]
3. [Use case 3]

**Features Provided:**
- [Feature with description]
- [Feature with description]

**Constraints:**
- [Constraint]
- [Performance requirement, e.g., "response time < 100ms"]
- [Availability requirement, e.g., "99.9% uptime"]

### 4. Architecture

**High-level Design:**
```
Input → Processing → Output
```

**Key Components:**
- [Component 1: Description]
- [Component 2: Description]

**Deployment:**
- [Number of instances]
- [Scaling strategy]
- [Failover mechanism]

### 5. API Specification

#### REST Endpoints

```
[HTTP METHOD] /api/v1/[resource]
Description: [What this endpoint does]
Authentication: [Required JWT scope]
Rate Limit: [X requests per minute]

Request:
{
  "field1": "type and description",
  "field2": "type and description"
}

Response (200):
{
  "id": "uuid",
  "field1": "value",
  "field2": "value",
  "createdAt": "ISO-8601 timestamp"
}

Error Responses:
400 Bad Request: [Description]
401 Unauthorized: [Description]
403 Forbidden: [Description]
404 Not Found: [Description]
429 Too Many Requests: [Description]
500 Internal Server Error: [Description]
```

#### Webhooks (if applicable)

```
Event: [event.name]
When: [When this event is triggered]
Payload:
{
  "event": "event.name",
  "timestamp": "ISO-8601",
  "data": { ... }
}
```

### 6. Data Model

**Primary Entities:**

```
EntityName {
  id: uuid (primary key)
  field1: type (description)
  field2: type (description)
  relationships: [references to other entities]
  indexes: [fields to index for performance]
  constraints: [unique, not null, etc.]
}
```

**Relationships:**
```
Entity1 --[relationship]--> Entity2
```

**Data Retention:**
- [How long data is stored]
- [Archive policy]
- [Deletion policy]

### 7. Dependencies

**Internal Services Used:**
- [Service name]: [Why and how it's used]
- [Service name]: [Why and how it's used]

**External APIs:**
- [API name]: [Why and how it's used]
- [API rate limits]: [X per minute/day]

**Databases:**
- [PostgreSQL tables]
- [Redis keys]
- [Elasticsearch indexes]

**Third-party Libraries:**
- [Library]: [Version and purpose]

### 8. Security & Authentication

**Access Control:**
- [Who can call this service]
- [JWT scopes required]
- [Role-based permissions]

**Input Validation:**
- [What inputs are validated and how]
- [Rate limiting strategy]
- [Injection prevention measures]

**Authorization:**
- [How ownership is verified]
- [Cross-organization isolation]
- [Resource-level permissions]

**Threat Mitigation:**
- [Specific threats and mitigations]
- [DDoS protection]
- [Brute force protection]

### 9. Privacy & Data Protection

**Data Classification:**
- [What data is stored and its sensitivity level]
- [PII handling]
- [Sensitive data fields]

**Encryption:**
- [Encryption at rest strategy]
- [Encryption in transit (always TLS 1.3)]
- [Key management]

**Data Retention:**
- [How long data is retained]
- [Archival policy]
- [Deletion process and verification]

**User Rights:**
- [How users can access their data]
- [How users can correct data]
- [How users can request deletion]
- [Data portability support]

**GDPR Compliance:**
- [How service implements GDPR rights]
- [Data processing documentation]
- [Privacy impact assessment]

### 10. Monitoring & Observability

**Key Metrics:**
- [Metric 1]: [Why it matters, alert threshold]
- [Metric 2]: [Why it matters, alert threshold]

**Health Checks:**
- [Health check endpoint]
- [Expected response]
- [Failure criteria]

**Logging:**
- [Log levels and verbosity]
- [Structured logging format]
- [Sensitive data masking]

**Alerting:**
- [Alert 1]: [Condition and severity]
- [Alert 2]: [Condition and severity]

**Debugging:**
- [How to enable debug mode]
- [Useful debug queries]
- [Common issues and solutions]

### 11. Testing Strategy

**Unit Tests:**
- [What functionality is unit tested]
- [Coverage target: 80%+]

**Integration Tests:**
- [Service interactions tested]
- [Mock external services]
- [Database interactions]

**E2E Tests:**
- [User workflows tested]
- [Failure scenarios]

**Load Testing:**
- [Expected load]
- [Performance targets]
- [Bottlenecks]

### 12. Deployment & Operations

**Prerequisites:**
- [Infrastructure requirements]
- [Environment variables]
- [Secrets needed]

**Deployment Process:**
- [How to deploy]
- [Rollback procedure]
- [Health check verification]

**Maintenance:**
- [Backup strategy]
- [Recovery procedure]
- [Scheduled maintenance windows]

### 13. Performance & Scalability

**Performance Targets:**
- [API response time target]
- [Throughput target]
- [Database query performance]

**Scaling Strategy:**
- [Horizontal scaling approach]
- [Vertical scaling limits]
- [Caching strategy]
- [Database sharding (if applicable)]

**Bottlenecks & Solutions:**
- [Identified bottleneck 1 and mitigation]
- [Identified bottleneck 2 and mitigation]

### 14. Future Roadmap

**Planned Features:**
- [Feature 1, target date]
- [Feature 2, target date]

**Technical Debt:**
- [Known issue 1 and plan]
- [Known issue 2 and plan]

**Migration Path:**
- [If service is being replaced, migration plan]

---

## Example: Identity Service Specification

```yaml
Service Name: Identity Service
Service Code: srv-identity
Team Ownership: Platform
Status: Production
Version: 1.0.0
Last Updated: 2026-07-06
```

### Purpose & Principles

**One-line purpose:**
Centralized authentication and authorization for all AIG services.

**Responsibilities:**
- Issue and validate JWT tokens
- Register and manage users
- Manage organizations and teams
- Assign and enforce roles and permissions
- Not responsible for: Encryption key management (Security Service), audit logging (Audit Service)

### Functional Requirements

**Primary Use Cases:**
1. User registration with email/password
2. User login and token issuance
3. Organization creation by users
4. Team member invitation and role assignment
5. Permission checking for protected resources

**Features:**
- User account management
- OAuth integration (Google, GitHub, Microsoft)
- Multi-factor authentication
- Role-based access control (RBAC)
- Organization isolation

### API Specification

#### User Registration
```
POST /api/v1/auth/register
Description: Register a new user
Authentication: None (public endpoint)

Request:
{
  "email": "user@example.com",
  "password": "securePassword123",
  "name": "John Doe"
}

Response (201):
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe",
  "organizationId": "uuid",
  "role": "owner",
  "createdAt": "2026-07-06T10:00:00Z"
}
```

#### User Login
```
POST /api/v1/auth/login
Description: Authenticate user and return JWT token
Authentication: None (public endpoint)
Rate Limit: 10 per minute (brute force protection)

Request:
{
  "email": "user@example.com",
  "password": "securePassword123"
}

Response (200):
{
  "accessToken": "jwt.token.here",
  "refreshToken": "jwt.refresh.token",
  "expiresIn": 900,
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "owner"
  }
}

Error Responses:
401 Unauthorized: Invalid email or password
429 Too Many Requests: Too many login attempts
```

### Security & Authentication

**Access Control:**
- Public endpoints: register, login, refresh token
- Protected endpoints: require valid JWT token
- Org-specific endpoints: verify organizationId in token matches

**Input Validation:**
- Email format validation
- Password strength requirements (min 12 chars, uppercase, number, special char)
- SQL injection prevention via parameterized queries
- Rate limiting: 10 login attempts per 15 minutes per IP

**Authorization:**
- Users can only manage resources in their organization
- Admins can manage team members
- Cross-organization data is never leaked

### Privacy & Data Protection

**Data Classification:**
- Email: PII (personally identifiable)
- Password: Highly sensitive (never stored in plain text)
- organizationId: Internal identifier (not sensitive)

**Encryption:**
- Passwords hashed with Argon2id (memory-hard, CPU-hard)
- JWT tokens signed with RS256
- All API calls over TLS 1.3

**Data Retention:**
- User account data: Retained indefinitely (unless deletion requested)
- Login logs: Retained for 90 days
- Token: Expires after 15 minutes (access token) or 7 days (refresh token)

**User Rights:**
- Users can download their account data
- Users can delete their account (removes all associated data)
- Users can request data portability

### Monitoring & Observability

**Key Metrics:**
- `auth_login_success_count`: Successful logins (alert if drops 50% below baseline)
- `auth_login_failure_count`: Failed login attempts (alert if exceeds 100/min)
- `auth_token_creation_latency`: Time to create JWT (alert if > 50ms)
- `auth_service_uptime`: Percentage of successful requests (alert if < 99.5%)

**Health Checks:**
```
GET /api/v1/health/ready
Response: { "status": "ready", "timestamp": "2026-07-06T10:00:00Z" }
```

**Logging:**
```
- Level INFO: User login, registration, token refresh
- Level WARN: Failed login, expired token
- Level ERROR: Database errors, internal failures
- Mask: Always mask passwords, tokens in logs
```

---

## How to Use This Template

1. **Copy this template** for a new service specification
2. **Fill in each section** with specific details
3. **Review with team** before implementation
4. **Update as service evolves** (keep it as source of truth)
5. **Reference in PRs** ("Implemented per section 5.1.3 of Identity Service spec")

Every service specification becomes the contract between developers and the product.

If the implementation doesn't match the spec, update the spec and get agreement before proceeding.

This discipline ensures consistency across the platform.

