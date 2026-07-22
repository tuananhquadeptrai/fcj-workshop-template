---
title: "Week 11 Worklog"
date: 2024-01-01
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Build Unit and Integration Tests for Cognito/JWT authentication.
* Develop the GPS Proximity Service and Anti-Fraud configuration.
* Update CI to run database migrations and the complete test suite automatically.

**Duration:** June 29, 2026 - July 3, 2026

### Learning content this week:
| Day | Learning content | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Monday | Wrote CognitoIdentityProvider Unit Tests and a JWT generation helper | 06/29/2026 | 06/29/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| Tuesday | Tested valid, expired, wrong-issuer, wrong-client_id, invalid-signature, and truncated JWT cases | 06/30/2026 | 06/30/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| Wednesday | Wrote Express Integration Tests for login and User Profile with valid, SUSPENDED, and missing-token cases | 07/01/2026 | 07/01/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| Thursday | Built a GPS Proximity Service with the Haversine formula, input validation, and a configurable 200-meter Anti-Fraud threshold | 07/02/2026 | 07/02/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| Friday | Added 13 GPS tests, reached 80 passing Unit Tests, updated GitHub Actions for migrations and the full suite, and improved the test factory | 07/03/2026 | 07/03/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |

### Week 11 Achievements:

* Completed JWT authentication and login/User Profile testing.
* Built a validated and configurable GPS Proximity Anti-Fraud service.
* Reached 80 passing Unit Tests and automated migration and test execution in CI.
