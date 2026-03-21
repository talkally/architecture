# ADR-021: Email Delivery via Amazon SES with Geo-Location Routing

**Date:** March 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly has been using a No-IP SMTP relay for outbound email since early development. It was never meant to stay — it was a POC-level integration to get something working during the initial build. By the time closed testing started on Google Play, it was clear the deliverability was inconsistent and there was no visibility into bounces or failures at all. A few test emails landed in spam during the tester onboarding phase, which was embarrassing.

There are two separate places in the product that send email. The Flutter app/backend handles transactional messages directly — things like OTP, welcome emails, and account notifications. n8n on the Hostinger VPS handles a different set — workflow-triggered sends like reminders and alerts. Both were going through the same No-IP relay, which meant one failure point for everything.

With TalkAlly targeting multiple markets (AU, UK, US, EU), I also started thinking about data residency. Routing all email through a single US-based relay regardless of where the user is located is a problem — not just technically but for GDPR and Australian Privacy Act compliance down the line. A UK user's transactional email shouldn't be processed in the US if it doesn't have to be.

---

## What Was Decided

Replace No-IP SMTP with Amazon SES, using regional endpoints matched to the user's country. The user's country is stored in their profile (set during onboarding, confirmed by the user) and drives which SES SMTP endpoint is used at send time. Both sending paths — TalkAlly app and n8n — connect to SES independently using separate IAM credentials.

The region mapping covers all current and planned TalkAlly markets:

| Market cluster | Countries | SES region | SMTP endpoint |
|---|---|---|---|
| Australia / Oceania | AU, NZ | `ap-southeast-2` | `email-smtp.ap-southeast-2.amazonaws.com` |
| United Kingdom | GB | `eu-west-2` | `email-smtp.eu-west-2.amazonaws.com` |
| Ireland / Iberia | IE, ES, PT | `eu-west-1` | `email-smtp.eu-west-1.amazonaws.com` |
| France / Benelux | FR, BE, NL, LU | `eu-west-3` | `email-smtp.eu-west-3.amazonaws.com` |
| Germany / Central & Eastern EU | DE, AT, CH, IT, PL, CZ, SK, HU, RO, BG, HR, SI, RS, BA, UA, GR, AL, MK | `eu-central-1` | `email-smtp.eu-central-1.amazonaws.com` |
| Nordics / Baltics | SE, NO, DK, FI, IS, EE, LV, LT | `eu-north-1` | `email-smtp.eu-north-1.amazonaws.com` |
| United States | US | `us-east-1` | `email-smtp.us-east-1.amazonaws.com` |
| Canada | CA | `ca-central-1` | `email-smtp.ca-central-1.amazonaws.com` |
| Southeast Asia | SG, MY, ID, TH, VN, PH | `ap-southeast-1` | `email-smtp.ap-southeast-1.amazonaws.com` |
| Japan | JP | `ap-northeast-1` | `email-smtp.ap-northeast-1.amazonaws.com` |
| India | IN | `ap-south-1` | `email-smtp.ap-south-1.amazonaws.com` |
| Latin America | BR, AR, CL, CO, MX | `sa-east-1` | `email-smtp.sa-east-1.amazonaws.com` |
| Default / rest of world | all others | `us-east-1` | `email-smtp.us-east-1.amazonaws.com` |

Italy and Switzerland are a known edge case — AWS has SES regions in Milan (`eu-south-1`) and Zurich (`eu-central-2`) but neither supports SMTP endpoints as of March 2026. Both are routed to Frankfurt. If strict in-country data residency becomes a requirement for those markets, this decision needs revisiting with SES API v2 direct integration.

Country detection works as follows: device locale (`Platform.localeName`) is read at first launch to pre-fill the onboarding country picker. Timezone serves as a secondary signal. The user confirms or changes the country, and their selection is saved to `UserProfile.country`. That stored value — not real-time location — is what drives routing at send time. A UK user travelling in Singapore still sends through `eu-west-2`. This is intentional.

Two separate IAM users are provisioned — `talkally-app-ses` for the Flutter backend and `talkally-n8n-ses` for n8n workflows. Both are scoped to `ses:SendEmail` on the `talkally.ai` identity only. Credentials are stored in Flutter environment config and n8n's credentials store respectively, not in source code.

Rollout is phased by market launch. AU (`ap-southeast-2`) activates first to replace No-IP. UK, US, and CA follow when those markets open. EU regions are batched together.

---

## What I Considered

SendGrid was the obvious commercial alternative — better documentation, built-in analytics, marketing features. The pricing is the problem. At $0.10 per 1,000 emails, SES is roughly 5-10× cheaper than SendGrid at comparable volume. For a product still in closed testing with an unknown email volume trajectory, that difference matters. SendGrid also doesn't offer per-region geo-routing at the SMTP level in the same way.

Postmark came up in the evaluation — excellent deliverability reputation for transactional email, but the cost is even higher than SendGrid and there's no regional routing story.

I briefly considered keeping No-IP as a fallback during transition but decided against it. Running two SMTP paths simultaneously adds complexity for no real benefit. The transition to SES is clean — one change in the credential config per sender.

One thing I'm not doing yet: SES Global Endpoints (multi-region endpoint / MREP). That feature provides automatic failover between two regions if one has an outage, which sounds useful. But it's a resilience feature, not a geo-routing feature — it doesn't route by user location. I'll revisit it when we have production send volumes that justify the operational overhead.

---

## Cost

At $0.10 per 1,000 emails after free tier (3,000 messages/month for the first 12 months), the cost for TalkAlly's current testing volumes is effectively zero. At scale — say 100,000 emails/month across all regions — total SES cost would be around $10/month. Each activated region is independent; there's no cross-region fee for the routing logic itself since that lives in app config and n8n, not in AWS.

---

## Consequences

The main operational cost is the per-region setup checklist: verify `talkally.ai` domain identity, configure Easy DKIM in Cloudflare DNS, request sandbox exit (takes 24-48 hours per region — needs to be planned ahead of market launches), generate SMTP credentials for both IAM users. This is a one-time overhead per region, not ongoing.

Bounce handling and complaint tracking are now available via SES event notifications through SNS/CloudWatch. This wasn't possible with No-IP at all. The observability improvement alone was worth the migration.

One concern I have not fully resolved: the `UserProfile.country` field needs to exist and be populated correctly for routing to work. If a user skips or dismisses the onboarding country picker, the app falls back to `us-east-1`. That's acceptable for now but should be revisited before scaling to EU markets where routing to a US region would be a compliance issue.

---

## Related Decisions
- ADR-003: n8n on Hostinger VPS as backend orchestration layer
- ADR-017: n8n webhook for email delivery
