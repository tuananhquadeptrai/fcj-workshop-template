---
title: "Anti-Fraud Engine & Risk Scoring"
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### 1. Introduction to the Anti-Fraud Engine
The key innovation of the **TrustBite** platform is its automated receipt-based risk scoring system, which verifies the authenticity of food reviews.

The anti-fraud verification pipeline consists of **three primary defense layers**, calculating a consolidated Fraud Risk Score to determine automated approval or trigger manual review.

---

### 2. Defense Layer 1: Duplicate File Hash Check (SHA-256)
The system prevents users from reusing previously uploaded receipt images.

Before executing OCR, the backend computes the **SHA-256** hash of the uploaded image file and checks for duplicates in PostgreSQL:
```javascript
import crypto from 'node:crypto';

export function computeFileHash(buffer) {
  return crypto.createHash('sha256').update(buffer).digest('hex');
}
```
*   **Rule:** If a hash collision is detected, the receipt is immediately flagged with the highest risk score (**+100 risk points**) and the review status is set to **REJECTED**.

---

### 3. Defense Layer 2: GPS Proximity Check (Haversine & PostGIS)
The system compares the user's GPS coordinates at the time of review submission with the restaurant's actual coordinates in the database.

The spherical distance is calculated using the **Haversine formula** in `server/src/services/receiptVerificationScoring.js`:

```javascript
const EARTH_RADIUS_METERS = 6371000;

export function haversineMeters(lat1, lng1, lat2, lng2) {
  const dPhi = toRad(lat2 - lat1);
  const dLambda = toRad(lng2 - lng1);
  const phi1 = toRad(lat1);
  const phi2 = toRad(lat2);

  const a = Math.sin(dPhi / 2) ** 2 + Math.cos(phi1) * Math.cos(phi2) * Math.sin(dLambda / 2) ** 2;
  return 2 * EARTH_RADIUS_METERS * Math.asin(Math.min(1, Math.sqrt(a)));
}
```
*   **Scoring Rules:**
    *   Distance **<= 200 meters**: Valid (**+0 risk points**).
    *   Distance **> 200 meters** (submitted on-site within 1 hour): **+40 risk points**.
    *   Distance **> 200 meters** (submitted remotely after 1 hour): **+10 risk points**.
    *   GPS disabled/denied: **+30 risk points**.

#### Spatial Queries Optimization via PostGIS on PostgreSQL:
In the production database design, **restaurants** and **restaurant_branches** tables store spatial coordinate points using the **GEOGRAPHY(Point, 4326)** data type, indexed with **GIST** to query nearest restaurants extremely quickly:
```sql
CREATE INDEX idx_restaurants_geo ON restaurants USING Gist(geo);
```
SQL queries leverage **ST_DWithin** and **ST_Distance** functions to perform precise distance calculations utilizing the index:
```sql
SELECT id, name, ST_Distance(geo, ST_MakePoint($1, $2)::geography) as distance
FROM restaurants
WHERE ST_DWithin(geo, ST_MakePoint($1, $2)::geography, $3);
```

---

### 4. Defense Layer 3: Restaurant Name Match via Levenshtein Distance
The merchant name extracted by AWS Textract is compared to the restaurant's name in the database. Because receipts might be blurry or OCR can miss characters, we use **Levenshtein similarity** rather than exact matching.

1.  **Text Normalization:** Strip Vietnamese diacritics, convert to lowercase, drop common business suffixes (co. ltd, branch, etc.), remove punctuation, and collapse whitespace:
    ```javascript
    export function normalizeMerchantName(name) {
      return name
        .normalize('NFD').replace(/[\u0300-\u036f]/g, '')
        .replace(/đ/gi, 'd')
        .toLowerCase()
        .replace(/\bco\.?\s*,?\s*ltd\.?\b/g, ' ')
        .replace(/[^a-z0-9\s]/g, ' ')
        .replace(/\s+/g, ' ').trim();
    }
    ```
2.  **Levenshtein Similarity Percentage:** Returns a similarity rating from 0% to 100%:
    *   Similarity **80% - 100%**: High match (**+0 risk points**).
    *   Similarity **60% - 79%**: Medium match (**+25 risk points**).
    *   Similarity **< 60%**: Low match (**+60 risk points**).
    *   Unreadable restaurant name: **+50 risk points**.

---

### 5. Risk Scoring Consolidation & Automated Decisions
The **scoreReceipt** function sums up the risk points, and **decideFromScore** determines the outcome:

*   **Score 0 - 30 (Low Risk):** Automatically verified (**VERIFIED**).
*   **Score 31 - 60 (Medium Risk):** Enqueued for manual review (**PENDING_ADMIN_REVIEW**).
*   **Score 61 - 99 (Elevated Risk):** Allowed public display but marked with low trust (**REFERENCE_ONLY**).
*   **Score >= 100 (Fraud Detected):** Rejected and hidden from public display (**REJECTED**).

> [!IMPORTANT]
> **Screenshot Checkpoint:**
> Run the unit tests verifying the anti-fraud scoring rules:
> ```bash
> npm run server:test:unit -- tests/unit/receipt/receiptVerificationScoring.test.js
> ```
> * **Screenshot 18:** Successful unit test execution showing all Haversine, normalization, Levenshtein, and scoring tests passed.

<img src="/images/5-Workshop/5.7-Anti-Fraud-Engine/18-anti-fraud-unit-tests.png" alt="Successful TrustBite anti-fraud rules and risk scoring unit test results" style="width: 100%; max-width: 1200px; height: auto;">
