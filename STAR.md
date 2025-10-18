# Tesco Direct Bundled product pricing discrepancy issue

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




