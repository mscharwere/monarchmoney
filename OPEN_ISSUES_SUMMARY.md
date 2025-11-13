# Monarch Money Python Library - Open Issues Summary
*Generated: 2025-11-13*

## Executive Summary

There are **30 open issues** spanning from November 2023 to November 2025. Issues fall into five main categories:
1. **Authentication/Session Problems** (highest priority - affecting users)
2. **API Compatibility & Breaking Changes**
3. **Feature Requests** (new functionality)
4. **Bug Fixes** (existing functionality)
5. **Code Quality & Testing**

---

## Critical Issues Requiring Immediate Attention

### 🔴 #172: gql 4.0.0 Compatibility Breaking Change
**Status:** Open since Nov 7, 2025
**Priority:** Critical - Blocking users
**Issue:** After upgrading to `gql==4.0.0`, `mm.get_transactions()` raises `TypeError: Client.execute_async() missing 1 required positional argument: 'request'`
**Workaround:** Downgrade to `gql<3.5.0`
**Impact:** Users cannot use the library with latest gql version
**Comments:** None yet
**Action Needed:** Update code to be compatible with gql 4.0+ API changes

### 🔴 #156: HTTP 404 on Login (Critical Auth Issue)
**Status:** Open since Aug 19, 2025
**Priority:** Critical - Authentication completely broken for some users
**Issue:** All login attempts fail with HTTP 404. Monarch changed their API endpoints.
**Discussion Highlights:**
- **techdoutdev (Aug 26):** Workaround involves extracting tokens from browser localStorage and manually creating session pickle file
- **keithah (Aug 26):** Fixed in PR #157 - missing auth headers (device-uuid, Origin) and incorrect email OTP field names
- **keithah (Sep 2):** Created enhanced fork with GraphQL fallback, proper headers, OTP detection, retry logic, and tests
- **ejlevin1 (Sep 12):** Asked whether to use keithah's fork or wait for official release
- **keithah (Sep 12):** Original maintainer unavailable, commits to maintaining enhanced fork at https://github.com/keithah/monarchmoney-enhanced
- **ianrtracey (Nov 12):** Supportive of keithah's work

**Status:** Community fork available but official library not updated
**Action Needed:** Merge PR #157 or incorporate fixes from keithah's fork

### 🔴 #139: Sessions Expire in 30-60 Minutes
**Status:** Open since Feb 23, 2025
**Priority:** High - Major usability issue
**Issue:** Sessions expire after 30 minutes to 1 hour, requiring frequent re-authentication
**Discussion Highlights:**
- **Multiple users (Feb-Mar):** Confirmed short expiration times
- **patcox (Aug 21):** API returns `tokenExpiration` set to 1 hour, but browser sessions have `tokenExpiration=null` (indefinite)
- **bradleyseanf (Oct 10):** Implemented fix requiring `trusted_device=True` to obtain non-expiring tokens
- **bradleyseanf (Oct 16):** Confirms fix stable after one week of testing

**Solution Identified:** Use `trusted_device=True` parameter
**Action Needed:** Update library to support trusted device parameter and document properly

### 🔴 #137: "Multi-Factor Auth Required" Due to App Update Warning
**Status:** Open since Feb 15, 2025
**Priority:** High - Intermittent authentication failures
**Issue:** Login fails with error indicating app update requirement, despite MFA disabled
**Discussion Highlights (16 comments):**
- **KOEPKEJR:** 50% failure rate at 6-hour intervals
- **elsell:** "EMAIL_OTP_REQUIRED" error without 2FA enabled
- **tcr (Feb 23):** Solution: logout via web UI, add "Device-UUID" header to API requests
- **hamiltont:** Confirms tcr's solution; Monarch uses client-generated UUIDs for device fingerprinting
- **tcr/hamiltont (Mar 7):** UUID can be extracted from GraphQL requests or `localStorage.getItem('monarchDeviceUUID')`
- **hamiltont (Mar 15):** Recommends enabling MFA and providing OTP seed for automation
- **NodeJSmith:** Shares implementation using MFA credentials stored as secrets

**Workarounds Available:** Device UUID extraction or MFA automation
**Action Needed:** Library should handle device UUID automatically

---

## API Changes & Compatibility Issues

### 🟡 #170: Show Transactions from All Accounts by Default (PR)
**Status:** Open since Oct 31, 2025
**Type:** Pull Request addressing Monarch API changes
**Issue:** Monarch changed transaction visibility; PR adds `hiddenByAccount` field and displays all transactions by default
**Comments:** None
**Action Needed:** Review and merge PR

### 🟡 #169: Change URL to New Domain
**Status:** Open since Oct 30, 2025
**Issue:** Update domain from monarchmoney.com to monarch.com
**Comments:** None
**Action Needed:** Update all domain references in code

### 🟡 #168: Add transactionVisibility Parameter
**Status:** Open since Oct 25, 2025
**Issue:** Monarch API now requires `transactionVisibility` parameter set to `all_transactions` to retrieve hidden transactions
**Comments:** None
**Action Needed:** Add parameter with appropriate default for backward compatibility

### 🟡 #129: get_transactions HTTP 403 Failure [LABELED AS FIXED]
**Status:** Labeled "fix_released" but still open since Jan 29, 2025
**Issue:** Calls to `get_transactions` fail with HTTP 403
**Discussion Highlights (23 comments):**
- **hammem (Jan 29):** Two causes: invalidated tokens and missing headers. Fixed in v0.1.15 - delete sessions and re-login
- **Multiple users:** Issues in Jupyter/Colab environments
- **codemonkey-design (Jan 31):** Monarch blocks VPN traffic
- **atkaiser:** HTTP 404 only during VPN login; operations work on VPN after authentication
- **codemonkey-design (Feb 18):** Monarch support confirmed "WAF may be blocking GraphQL requests when using VPN"
- **bradyespey (Mar 27):** Resolved by using token from localStorage instead of cookies

**Status:** Partially resolved but VPN blocking remains
**Action Needed:** Document VPN limitations and localStorage token method

---

## Feature Requests

### High Priority Features

#### #162: Manage Transaction Rules Programmatically
**Status:** Open since Sep 2, 2025
**Request:** Ability to manage transaction rules via API for LLM-based agents and MCP servers
**Discussion:**
- **keithah (Sep 4):** Fork implements this: https://github.com/keithah/monarchmoney-enhanced with MCP wrapper
- **bdsoha (Oct 26):** Also needed for manual transactions via API to apply custom rules

**Action Needed:** Review keithah's implementation for potential merge

#### #165: Add get_recurring_transaction_streams Method
**Status:** Open since Sep 19, 2025
**Request:** New method using Web_GetAllRecurringTransactionItems query to return accurate live amounts, including credit card payment streams with real balances
**Comments:** None
**Action Needed:** Evaluate and implement if valuable

#### #151: Add Holdings-Related Functionality (PR)
**Status:** Open since Jun 5, 2025
**Type:** Pull Request
**Request:** Methods to retrieve holding IDs by ticker, manually add holdings, and delete holdings
**Discussion:**
- **gabevf (Sep 3):** "would love to be able to update holding amounts as well"

**Related:** See also #112 (original request for addHolding API)
**Action Needed:** Review PR, consider adding update functionality

#### #148: Support Filter Transactions by Amount
**Status:** Open since May 18, 2025
**Request:** Filter capabilities for transaction amounts (credit/debit filtering, range-based searches)
**Comments:** None
**Action Needed:** Implement alongside #106

#### #150: Missing getMe API Capabilities
**Status:** Open since Jun 3, 2025
**Request:** `getMe` API method for retrieving user settings, particularly timezone information
**Comments:** None
**Action Needed:** Implement user settings endpoint

#### #154: Add Support for Updating Flexible Budget
**Status:** Open since Jul 22, 2025
**Request:** No description provided
**Comments:** None
**Action Needed:** Request clarification from user

#### #134: [Feature Request] Merchants
**Status:** Open since Feb 3, 2025
**Request:** `get_merchant()` method since transactions reference merchants maintained in Monarch settings
**Related:** See #128 (adds underlying API calls for categories and merchants)
**Action Needed:** Implement alongside #128

#### #106: Add Additional Filters to get_transactions
**Status:** Open since May 13, 2024
**Labels:** enhancement, help wanted, good first issue
**Request:** Merchant and amount filtering for programmatic transaction matching against external sources
**Comments:** None
**Action Needed:** Good candidate for community contribution

#### #29: "Move" Money Between Budgets
**Status:** Open since Nov 9, 2023
**Labels:** enhancement, help wanted
**Request:** No description provided
**Comments:** None
**Action Needed:** Request clarification, consider closing if stale

### Medium Priority Features

#### #113: Currency Converter Tool (PR)
**Status:** Open since Jul 30, 2024
**Type:** Pull Request
**Request:** Currency conversion tool for transactions and balance history with state management and tagging
**Comments:** None
**Action Needed:** Review PR for merge

---

## Bug Fixes

### 🔧 #147: Don't Use Lists as Defaults in Function Definitions (PR)
**Status:** Open since May 5, 2025
**Type:** Pull Request - Code Quality
**Issue:** `get_transactions` uses empty lists for defaults, creating shared mutable state across calls
**Fix:** Use `None` instead of empty lists
**Comments:** None
**Action Needed:** Review and merge - standard Python best practice

### 🔧 #143: Force SSL Validation with aiohttp (PR)
**Status:** Open since Mar 16, 2025
**Type:** Pull Request
**Issue:** Fixes #142 - sets `ssl=True` when building AIOHTTPTransport
**Comments:** None
**Action Needed:** Review and merge

### 🔧 #142: SSL Certificates Not Verified Warning
**Status:** Open since Mar 16, 2025
**Issue:** UserWarning that AIOHTTPTransport doesn't verify SSL certificates by default
**Related:** Fixed by PR #143
**Action Needed:** Merge PR #143

### 🔧 #141: Await text to Enable Logging (PR)
**Status:** Open since Feb 27, 2025
**Type:** Pull Request
**Issue:** `resp.text` is a coroutine requiring `await` to obtain actual text value
**Comments:** None
**Action Needed:** Review and merge

### 🔧 #136: Fix Upload Account Balance History (PR)
**Status:** Open since Feb 13, 2025
**Type:** Pull Request
**Issue:** Improves balance history upload by adding parsing and status-checking methods
**Related:** See also #86 and #87
**Comments:** None
**Action Needed:** Review and merge

### 🔧 #87: upload_account_balance_history Not Working
**Status:** Open since Mar 3, 2024
**Labels:** bug
**Issue:** Function uploads file but doesn't update balances in Monarch Money, no errors reported
**Discussion:**
- **andrecloutier (Mar 7):** Monarch changed API endpoint, fix in PR #86
- **CFarzaneh (Mar 22):** Requests ETA for merge
- **hammem (Apr 14):** "Merges and new release coming shortly"

**Related:** PR #86 and PR #136 address this
**Action Needed:** Verify fix and close, or merge pending PRs

### 🔧 #86: Improve Upload Account Balance History (PR)
**Status:** Open since Mar 2, 2024
**Type:** Pull Request
**Issue:** Adds methods for parsing uploaded CSV and checking processing status
**Related:** Fixes #87
**Comments:** See #87 discussion
**Action Needed:** Review status - hammem indicated merge coming in April 2024

---

## Code Quality & Development

### 📚 #116: Externalize GraphQL Queries
**Status:** Open since Aug 21, 2024
**Type:** Code quality improvement
**Request:** Move GraphQL queries from inline code to external variables for readability and potential code generation
**Comments:** None
**Action Needed:** Consider for future refactoring

### 📚 #60: Add Integration Testing
**Status:** Open since Jan 5, 2024
**Labels:** enhancement
**Request:** Integration testing framework using dummy accounts for validation before releases
**Comments:** None
**Action Needed:** Set up test infrastructure

---

## Issue Statistics

**By Status:**
- Critical (blocking users): 4 issues (#172, #156, #139, #137)
- API Changes: 4 issues (#170, #169, #168, #129)
- Feature Requests: 11 issues
- Bug Fixes: 8 issues
- Code Quality: 2 issues

**By Age:**
- 2025: 26 issues
- 2024: 3 issues
- 2023: 1 issue

**Pull Requests Open:** 9 (#170, #151, #147, #143, #141, #136, #113, #86, likely more)

**Issues with Active Discussion:** 5 (#156, #162, #139, #137, #129)

---

## Recommended Action Plan

### Immediate (This Week)
1. **Fix gql 4.0.0 compatibility (#172)** - Critical blocker
2. **Merge authentication fixes from PR #157 or keithah's fork (#156)** - Critical auth issue
3. **Implement trusted_device parameter (#139)** - Solution identified and tested
4. **Update domain to monarch.com (#169)** - Simple but important

### Short Term (Next 2 Weeks)
5. **Review and merge ready PRs** - #147, #143, #141 (code quality fixes)
6. **Add transactionVisibility parameter (#168)** - API compatibility
7. **Merge transaction visibility PR (#170)** - API compatibility
8. **Document Device UUID and VPN limitations (#137, #129)** - Workarounds exist

### Medium Term (Next Month)
9. **Review holdings functionality PRs (#151, related to #112)**
10. **Implement transaction rules management (#162)** - High user interest
11. **Add transaction filtering (#148, #106)** - Multiple related requests
12. **Fix balance history upload (#87, #86, #136)** - Multiple PRs available

### Long Term
13. **Implement getMe API (#150)**
14. **Add merchants endpoint (#134, #128)**
15. **Currency converter (#113)**
16. **Set up integration testing (#60)**
17. **Externalize GraphQL queries (#116)**

---

## Community Engagement Notes

**Active Community Members:**
- **keithah** - Created enhanced fork with critical fixes, actively maintaining
- **bradleyseanf** - Identified trusted_device solution
- **hamiltont** - Contributed device UUID and MFA solutions
- **NodeJSmith** - Multiple contributions and code quality improvements

**Maintainer Status:**
- Original maintainer (hammem) appears less active based on comment in #156
- Community fork (keithah/monarchmoney-enhanced) has become de facto maintained version
- Consider reaching out to keithah about co-maintaining or merging improvements

**Repository Health Concerns:**
- Many PRs awaiting review (some since March 2024)
- Critical authentication issues remain unresolved in official repo
- Community creating forks due to lack of official updates
- Documentation needs updates for workarounds and new parameters
