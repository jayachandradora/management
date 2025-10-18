# 1. Tesco Direct Bundled product pricing discrepancy issue

### ✅ **S - Situation**

While working on the Tesco Direct website, which deals primarily with electronics products, we encountered a critical issue where incorrect (lower) prices were shown to some customers for bundled products. This discrepancy led to business losses, as products were sold below the intended price.

The problem did not occur uniformly across the European region but was reported for a subset of users. The accounting system flagged this issue after detecting revenue mismatches and alerted the engineering team, including detailed reports of affected transactions and customers.

---

### ✅ **T - Task**

My responsibilities included:

1. Investigating and reproducing the pricing discrepancy issue.
2. Identifying the root cause without impacting live services, especially since the pricing service is a critical component.
3. Collaborating with the accounting and backend teams to trace the issue using product IDs, user IDs, and transaction tracking data.
4. Implementing a fix and preventing future occurrences.
---

### ✅ **A - Action**
To resolve this issue, I took the following steps:

1. **Investigated Database and Service Layer:**

   * Analyzed the database and pricing retrieval mechanisms across regions to check for inconsistencies.
   * Sent pricing API requests individually to all pods (microservice instances) to reproduce the issue.

2. **Traced Specific Transactions:**

   * Used tracking IDs and ordering journeys provided by the accounting team to trace affected transactions.
   * Narrowed down the scope to specific servers and users.

3. **Root Cause Analysis:**

   * Discovered that the problem stemmed from an **in-memory cache implementation**. The pricing updates were only reflected at the JVM (instance) level, leading to stale data on other pods.
   * Pricing for bundled products (which are seasonal) wasn't getting updated across all instances, causing inconsistency.

4. **Fix Implementation:**

   * Immediately used **feature flags** to disable bundled pricing logic on impacted instances.
   * Restarted affected servers to refresh the stale cache data and mitigate further losses.

5. **Long-Term Fix:**

   * Replaced the in-memory cache with a **centralized Hazelcast distributed cache** to ensure consistency of pricing data across all pods.
   * Introduced **feature flag support** for bundled product pricing to enable safe toggling for seasonal promotions.

---

### ✅ **R - Result**

* Successfully identified and resolved the production issue without bringing down the pricing service.
* Mitigated financial losses and helped the business recover from further pricing anomalies.
* Delivered a long-term robust caching solution, improving system reliability and scalability.
* Gained appreciation from stakeholders for proactive communication, coordination with cross-functional teams, and delivering a fix before the deadline.

---

# 2. Product Feed & Feedback Service Resource Consumption Issue

---

## ✅ **STAR Format: Technical Challenge – Product Feed & Feedback Service Resource Consumption Issue**

### ⭐ **Situation:**

Our team manages a **Storm orchestration service** responsible for processing **product feeds from various vendors**. This service accepts feeds, processes them via several microservices, and posts the finalized data downstream to make products available for customers.

As part of the pipeline, once a product is ready for live deployment, a **Feedback Service** sends status updates to vendors regarding their product feed processing.

However, we encountered a **critical issue in production**:

* The **Feedback Service had an N+1 database select problem**.
* Combined with a new **product requirement** to send **consolidated feedback messages** (instead of individual messages), this led to **high memory usage and system crashes**, especially for **large product feeds (>100MB)**.

---

### 🎯 **Task:**

1. **Reproduce the issue** to understand and confirm its root cause.
2. **Determine the actual product feed size** that triggers the issue.
3. **Identify whether the problem could be simulated in lower environments**.
4. **Implement a fix** to avoid future crashes and meet product requirements.
5. **Coordinate with stakeholders** (vendors and product owners) to communicate limitations and prioritize resolution.

---

### ⚙️ **Action:**

1. **Analyzed logs and metrics** from production to isolate patterns in failing requests. Found crashes occurred only for feed sizes >100MB.
2. Faced challenges in **reproducing the issue**, as not all feeds had problems. Identified the **size threshold** as a key factor.
3. **Simulated large feed scenarios** in a lower environment to replicate memory consumption and verify the root cause.
4. **Temporarily disabled the Feedback Service functionality** in production via a feature flag to prioritize feed processing and avoid business impact.
5. **Reported the issue to vendors**, advising them to restrict product feed size to <100MB until the problem was resolved.
6. Engaged with product owners to **align priorities**, explaining that consolidated feedback was temporarily disabled for stability.
7. **Resolved the N+1 select issue** by optimizing database queries in the Feedback Service.
8. Re-enabled the feedback functionality post-fix and tested thoroughly in staging before deployment to production.

---

### 🏁 **Result:**

1. **System stability restored** in production with **no business loss or product feed processing delays**.
2. Vendors adhered to size restrictions temporarily, ensuring smoother operations.
3. **Successfully optimized the Feedback Service**, removing the N+1 query bottleneck.
4. **Feedback Service feature flag re-enabled** after validating the fix.
5. Team learned the importance of **feed size monitoring and scalable memory handling**, leading to longer-term architectural improvements.

---



