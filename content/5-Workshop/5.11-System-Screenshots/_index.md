---
title: "System Screenshots"
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

### 1. Admin Portal Interface Overview
The **TrustBite** administration portal is built with **Next.js** and **TailwindCSS**, providing a clear interface for operating the management, review, and system-monitoring features.

The following sections present the primary Admin Portal screens captured from the deployed environment.

---

### 2. Dashboard Overview
The central operations dashboard shows the API state, total restaurants, connected modules, and modules that are still waiting for API support. The lower panel summarizes the availability of each administrative capability.

The left sidebar groups the interface into the following areas:

* **Main:** Dashboard overview.
* **Management:** Restaurants and Users.
* **Trust:** Reviews and Verification.
* **System:** Audit Logs and Monitoring.

<img src="/images/5-Workshop/5.11-System-Screenshots/19-admin-dashboard.png" alt="TrustBite central operations dashboard showing API status, restaurant count, and administrative module availability" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Screenshot 19:** The Admin Portal command dashboard showing the online API, total restaurants, and the status of administrative modules.

---

### 3. Restaurant Management Screen
The restaurant management area lets administrators open a restaurant profile and update its operational information, including:

* Restaurant name, address, phone number, and status.
* **Latitude** and **Longitude** fields used for GPS proximity checks. In the captured screen, both fields are blank and can be completed when location data is available.
* Description, category, and an administrative change reason.
* The primary restaurant image and the detailed image library.

<img src="/images/5-Workshop/5.11-System-Screenshots/20-restaurant-management.png" alt="TrustBite restaurant detail modal with basic information, blank latitude and longitude fields, and restaurant image management controls" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Screenshot 20:** The restaurant detail editing modal showing editable information, blank GPS coordinate fields, and controls for the primary and detail images.

---

### 4. User Management Screen
The user management area lists system accounts and provides a detail view for each user. From the detail modal, an administrator can:

* Review and update the user's full name, phone number, and date of birth.
* Inspect account status, rank, and experience points (EXP).
* Enter an administrative reason before saving profile or status changes.
* Lock the account when necessary; the interface notes that this action revokes active sessions and invalidates refresh tokens.

<img src="/images/5-Workshop/5.11-System-Screenshots/21-user-account-management.png" alt="TrustBite user account detail modal showing profile information, active status, and the account lock action" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Screenshot 21:** The user detail interface for editing profile data, reviewing account status, and locking an account.

---

### 5. Review Management Screen
The review management screen organizes public review content by restaurant. Administrators can search for and select a restaurant in the left panel, then inspect its reviews in the main content area.

The screen provides:

1. The number of matching reviews, the current average rating, and the restaurant trust score.
2. Each review's overall rating and category ratings for food, price, service, and ambience.
3. Review status, such as **Reference**, together with the publication date and reaction counts.
4. A status filter for selecting which group of reviews to display.

<img src="/images/5-Workshop/5.11-System-Screenshots/22-review-management.png" alt="TrustBite review management screen showing restaurant selection, aggregate and category ratings, review status, and status filtering" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Screenshot 22:** Restaurant review management with aggregate ratings, category scores, **Reference** status, and the status filter.

---
