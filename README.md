# Revenue Leak OS

Production-ready SaaS for detecting and recovering revenue opportunities in service businesses.

## Overview

Revenue Leak OS helps service businesses (dental clinics, aesthetic clinics, appointment-based services) automatically detect and recover lost revenue opportunities:

- New leads that haven't received a response
- Missed calls
- Leads who stopped responding
- Abandoned quotes
- Cancelled appointments
- Dormant customers
- High-value opportunities requiring human attention

The system detects leaks, prioritizes them, recovers what's possible, and measures results.

**Tagline:** Stop losing revenue you already paid to generate.

## Quick Start

### Prerequisites

- Node.js 18+
- PostgreSQL 14+
- Redis 7+
- OpenAI API key
- Stripe account (test mode)

### Local Setup

```bash
# Clone repository
git clone https://github.com/92brjc54t7-glitch/revenue-leak-os.git
cd revenue-leak-os

# Install dependencies
npm install

# Copy environment template
cp .env.example .env.local

# Set up PostgreSQL
createdb revenue_leak_os_dev

# Run migrations
npm run db:migrate

# Seed demo data
npm run db:seed

# Start Redis
redis-server

# Start development server
npm run dev

# Access at http://localhost:3000
```

### Environment Variables

Create `.env.local`:

```
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/revenue_leak_os_dev

# Redis
REDIS_URL=redis://localhost:6379

# OpenAI
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4o-mini

# Stripe
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
SESSION_SECRET=generate-a-random-secret-here

# Email/SMS (optional integrations)
SENDGRID_API_KEY=
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=

# Demo mode
DEMO_MODE=false
```

## Architecture

### Core Components

1. **Lead Ingestion** - Webhook, REST API, CSV, manual entry
2. **AI Classification** - Lead analysis with safety guardrails
3. **Conversation Engine** - Intelligent response generation
4. **Revenue Leak Detection** - Background job identifying opportunities
5. **Workflow Engine** - Automated recovery sequences
6. **Human Handoff** - Escalation system for complex cases
7. **Revenue Attribution** - Tracking and measurement
8. **Dashboard** - Business analytics and insights
9. **Knowledge Base** - Fact verification layer
10. **Integrations** - Calendar, CRM, SMS, payments

### Tech Stack

- **Frontend:** Next.js 14, TypeScript, Tailwind CSS, Shadcn/UI
- **Backend:** Next.js API routes, TypeScript
- **Database:** PostgreSQL with Prisma ORM
- **Background Jobs:** BullMQ with Redis
- **AI:** OpenAI API with safety validation
- **Auth:** NextAuth.js or custom secure implementation
- **Payments:** Stripe
- **Deployment:** Docker, PostgreSQL, Redis

### Database Schema

Core tables:

- `users` - Application users
- `organizations` - Business accounts (multi-tenant)
- `business_profiles` - Service business configuration
- `leads` - Prospect/customer records
- `customers` - Existing customers
- `conversations` - Chat/communication threads
- `messages` - Individual messages
- `appointments` - Scheduled services
- `quotes` - Service estimates
- `revenue_events` - Financial transactions
- `recovery_opportunities` - Detected leaks
- `campaigns` - Automated recovery sequences
- `automation_rules` - Workflow configuration
- `knowledge_documents` - Business fact base
- `integrations` - Third-party connections
- `subscriptions` - Billing data
- `audit_logs` - Security/compliance

## Core Features

### 1. Lead Ingestion

**POST /api/v1/webhooks/leads**

Accept leads from any source and normalize to internal format:

```json
{
  "name": "John Doe",
  "phone": "+212612345678",
  "email": "john@example.com",
  "source": "website",
  "service_interest": "Dental cleaning",
  "message": "I need a check-up",
  "preferred_language": "fr"
}
```

### 2. AI Classification

Every lead is automatically analyzed:

```json
{
  "intent": "Book appointment",
  "service": "Dental cleaning",
  "urgency": "Medium",
  "temperature": "warm",
  "estimated_value": 150,
  "buying_stage": "Ready to book",
  "sentiment": "Positive",
  "language": "French",
  "requires_human": false,
  "recommended_action": "Send appointment options",
  "reason": "Customer has clear intent and urgency"
}
```

### 3. AI Response Generation

Generate contextual replies that sound natural and respect business policies:

```json
{
  "should_send": true,
  "message": "Bonjour John, Merci de votre intérêt pour un nettoyage dentaire...",
  "channel": "sms",
  "requires_approval": false,
  "reason": "Qualified warm lead, matches automation policy"
}
```

### 4. Revenue Leak Detection

Continuous background scanning identifies:

- **Type A:** Unanswered leads
- **Type B:** Missed calls
- **Type C:** Stalled conversations
- **Type D:** Abandoned quotes
- **Type E:** Abandoned bookings
- **Type F:** Cancellation recovery opportunities
- **Type G:** No-show recovery
- **Type H:** Dormant customer reactivation
- **Type I:** High-value opportunities

### 5. Automated Workflows

```
NEW LEAD
  ↓
AI CLASSIFY
  ↓
[Unsafe/Complex?] → HUMAN HANDOFF
  ↓ [No]
GENERATE RESPONSE
  ↓
SEND MESSAGE
  ↓
WAIT FOR RESPONSE
  ↓
[Response received?] → PROCESS & CONTINUE
[No response after 24h?] → FOLLOW-UP 1
[No response after 48h?] → FOLLOW-UP 2
[No response after 7 days?] → FOLLOW-UP 3 (FINAL)
[Still no response?] → STOP, MARK AS LOST
```

Automation stops if:
- Customer opts out
- Customer books
- Customer asks for human
- Human takes over
- Business disables automation

### 6. Revenue Attribution

Track every step:

- Lead created
- First contact made
- Lead qualified
- Quote sent
- Quote accepted
- Appointment booked
- Payment received
- Revenue recovered

Dashboard shows:

- **Potential Revenue:** Value of all open opportunities
- **Revenue at Risk:** Opportunities likely to be lost
- **Recovered Revenue:** Verified recovered amount
- **Recovery Rate:** Percentage of detected opportunities recovered
- **Average Recovery Value:** Per-lead average

### 7. Dashboard

Main dashboard answers:

**Where are we losing money?**
- Top 5 revenue leaks
- Total potential revenue
- Severity breakdown

**What should I do today?**
- New leads needing response
- Escalations requiring human attention
- Hot opportunities ready to close
- Morning briefing

Pages:
- `/dashboard` - Overview and KPIs
- `/leads` - Lead management
- `/conversations` - Unified inbox
- `/opportunities` - Recovery pipeline
- `/campaigns` - Automation campaigns
- `/customers` - Customer database
- `/appointments` - Calendar
- `/revenue` - Revenue tracking
- `/analytics` - Deep insights
- `/integrations` - Third-party connections
- `/knowledge` - Fact base management
- `/settings` - Configuration
- `/billing` - Subscription management

### 8. Knowledge Base

Businesses upload verified information:
- Service descriptions
- Pricing
- FAQs
- Policies
- Opening hours
- Staff bios
- Documents

AI only makes factual claims using this knowledge base. Never invents prices, availability, or medical claims.

### 9. Multilingual Support

- English
- French
- Arabic
- Moroccan Darija

Auto-detects customer language. Business can set preferred languages.

### 10. Security

- Tenant isolation (every query enforces tenant ownership)
- Secure authentication
- Password hashing (bcrypt)
- Session management
- Rate limiting
- Webhook signature verification
- Input validation
- CSRF protection
- Secure headers
- Audit logging
- Prompt injection protection

**Critical:** A user from Tenant A must NEVER see Tenant B's data.

### 11. Stripe Billing

**Plans:**
- STARTER: €99/month
- GROWTH: €249/month
- PRO: €499/month

Features:
- Subscription management
- Upgrade/downgrade
- Cancellation
- Billing portal
- Webhook handling
- Payment failure recovery

### 12. Integrations

Adapter pattern for extensibility:

- **MessagingProvider:** SMS, email, WhatsApp
- **CalendarProvider:** Google Calendar, Outlook
- **CRMProvider:** Custom systems, HubSpot
- **PaymentProvider:** Stripe, local processors

Initial implementations:
- Generic webhook
- Google Calendar
- Email (SendGrid)
- SMS (Twilio)
- Stripe
- Custom booking URLs

## Testing

Run test suite:

```bash
npm run test
```

Tests cover:
- Authentication and authorization
- Tenant isolation
- Lead ingestion and classification
- AI response validation
- Opt-out enforcement
- Human handoff
- Revenue attribution
- Stripe webhook processing
- Complete end-to-end workflows

## Deployment

### Docker

```bash
docker build -t revenue-leak-os .
docker-compose up
```

### Environment Setup

1. Create PostgreSQL database
2. Set all environment variables
3. Run migrations: `npm run db:migrate`
4. Configure Stripe webhooks
5. Set up OpenAI API key
6. Configure Redis for background jobs

### Production Checklist

- [ ] Database backed up
- [ ] Redis configured for persistence
- [ ] SSL/TLS enabled
- [ ] Security headers configured
- [ ] Rate limiting active
- [ ] Audit logging enabled
- [ ] Monitoring and alerting set up
- [ ] Stripe webhook secrets configured
- [ ] OpenAI API key secure
- [ ] Database credentials in secrets manager
- [ ] Session secret rotated
- [ ] Admin account created
- [ ] Email delivery tested
- [ ] SMS delivery tested (if used)

## API Documentation

### Authentication

All requests to `/api/v1/*` require authentication token:

```
Authorization: Bearer <jwt_token>
```

### Lead Ingestion

**POST /api/v1/webhooks/leads**

Webhook for receiving leads from any source.

**POST /api/v1/leads**

Create lead via API.

**GET /api/v1/leads**

List all leads for authenticated user's tenant.

### Conversations

**GET /api/v1/conversations**

List all conversations.

**POST /api/v1/conversations/{id}/messages**

Send message in conversation.

**POST /api/v1/conversations/{id}/handoff**

Escalate to human.

### Revenue

**GET /api/v1/revenue/summary**

Get revenue metrics.

**POST /api/v1/revenue/events**

Record revenue event.

## Demo Mode

Run with `DEMO_MODE=true` to load sample data:

- 20 demo leads
- 10 sample customers
- 5 conversations
- 5 recovery opportunities
- 3 appointments
- Sample revenue events

Demo data is isolated from real tenant data.

## Known Limitations

1. AI responses use standard models (not specialized medical models)
2. Phone verification not yet implemented
3. Video integration not included
4. Manual payment processing not implemented
5. Advanced reporting (custom dashboards) is basic

## Next Development Steps

1. Phone call recording and transcription
2. Video consultations
3. Advanced analytics and custom reports
4. Team collaboration features
5. Multi-location support
6. Advanced CRM integrations
7. Predictive analytics
8. A/B testing framework
9. White-label capabilities
10. Mobile app

## Security & Compliance

- GDPR-compliant data handling
- SOC 2 architecture
- Audit logging
- Tenant isolation enforcement
- PCI-DSS compliant payment handling
- Secure API design

## Support

- Documentation: See `/docs`
- Issues: GitHub Issues
- Security: report to security@example.com

## License

MIT

---

## Getting Help

1. Check `/docs` folder
2. Review test files for usage examples
3. Check `.env.example` for configuration
4. Run tests: `npm run test`

## Development

```bash
# Start dev server
npm run dev

# Run tests
npm run test

# Build
npm run build

# Database migrations
npm run db:migrate
npm run db:seed

# Check types
npm run type-check

# Lint
npm run lint
```

---

**Current Version:** 0.1.0
**Status:** Active Development
**Last Updated:** 2025
