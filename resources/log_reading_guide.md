# Web Activity Log Reading Guide

## Log File Structure

### Overview
```
IP_ADDRESS:
  TIME                     METHOD REQUEST                          STATUS
  2021-06-25T07:23:00.000Z GET    "/"                              401 (UNAUTHORIZED)
  2021-06-25T07:23:00.000Z GET    "/login"                         200 (SUCCESS)
```

---

## Understanding Each Component

### 1. IP Address Block
```
192.168.0.50:
```
- Each block represents ONE device
- IP addresses are internal (192.168.0.x)
- Static IPs (don't change)

### 2. Timestamp
```
2021-06-25T07:23:00.000Z
```
- Format: `YYYY-MM-DDTHH:MM:SS.mmmZ`
- UTC timezone (Z = Zulu/UTC)
- Millisecond precision (.000)

### 3. HTTP Method
```
GET    - Retrieve data
POST   - Submit data (like login)
```

### 4. Request URL
```
"/"                                           → Homepage
"/login"                                      → Login page
"/api/factory/status?factory=*"               → All factories
"/api/factory/machine/status?factory=meiyo"   → Specific factory
```

### 5. Authentication
```
{authorizedUserId: "5Eckr4DTaLLDaDMGqmMJ3g"}
```
- Appears after successful login
- Unique identifier for each user
- Present in all authenticated requests

### 6. Status Codes
```
200 (SUCCESS)      → Request succeeded
401 (UNAUTHORIZED) → Not logged in / session expired
```

---

## Normal User Session Example

```
192.168.0.50:
  2021-06-25T07:23:00.000Z GET    "/"              401 (UNAUTHORIZED)
  2021-06-25T07:23:00.000Z GET    "/login"         200 (SUCCESS)
  2021-06-25T07:23:00.000Z GET    "/login.css"     200 (SUCCESS)
  2021-06-25T07:23:00.000Z GET    "/login.js"      200 (SUCCESS)
  2021-06-25T07:23:44.000Z POST   "/login"         200 (SUCCESS)
  2021-06-25T07:23:45.000Z GET    "/" {...}        200 (SUCCESS)
  2021-06-25T07:23:45.000Z GET    "/index.css" ... 200 (SUCCESS)
  2021-06-25T07:23:45.000Z GET    "/index.js" ...  200 (SUCCESS)
  2021-06-25T07:23:46.000Z GET    "/api/factory/status?factory=*" {...} 200
```

### Session Flow:
1. **Initial Access** (401) → User not logged in
2. **Login Page** → Load login form + resources
3. **Authentication** (POST) → Submit credentials
4. **Dashboard** → Load main page + resources
5. **API Queries** → Get factory/machine data

---

## What to Look For

### 🟢 Normal Behavior Indicators

1. **Resource Loading Sequence**
   ```
   GET /login
   GET /login.css
   GET /login.js
   POST /login
   GET /
   GET /index.css
   GET /index.js
   ```
   - Pages load with their stylesheets and scripts
   - Natural progression

2. **Variable Timing**
   ```
   07:23:00 → Login page
   07:23:44 → Submit login (44 seconds later)
   07:23:45 → Dashboard (1 second later)
   07:24:01 → API call (16 seconds later)
   ```
   - Human thinking time
   - Irregular intervals

3. **Targeted Queries**
   ```
   GET /api/factory/machine/status?factory=meiyo&machine=LaserWelder
   ```
   - Specific factory and machine
   - Not querying everything

4. **Session Management**
   ```
   Day 1: Login → Activity → End
   Day 2: Login again → Activity → End
   ```
   - Re-authenticates on new days
   - Respects session expiration

### 🔴 Suspicious Behavior Indicators

1. **Exact Time Intervals**
   ```
   17:00:48 → First request
   18:00:48 → Second request (exactly 3600s later)
   19:00:48 → Third request (exactly 3600s later)
   ```
   - Perfect precision = automation
   - Not humanly possible

2. **No Page Resources**
   ```
   17:00:48 GET /api/factory/machine/status?factory=meiyo&machine=*
   18:00:48 GET /api/factory/machine/status?factory=meiyo&machine=*
   ```
   - Direct API calls only
   - No CSS/JS loading
   - Not using the web interface

3. **Simultaneous Queries**
   ```
   17:00:48 GET /api/.../factory=meiyo&machine=*
   17:00:48 GET /api/.../factory=seiko&machine=*
   17:00:48 GET /api/.../factory=berlin&machine=*
   17:00:48 GET /api/.../factory=shenzhen&machine=*
   ```
   - Same exact timestamp
   - All factories at once
   - Automated script behavior

4. **Ignoring Session Expiration**
   ```
   23:00:48 GET /api/... 200 (SUCCESS)
   00:00:48 GET /api/... 401 (UNAUTHORIZED)
   01:00:48 GET /api/... 401 (UNAUTHORIZED)
   02:00:48 GET /api/... 401 (UNAUTHORIZED)
   ```
   - Session expired at midnight
   - Bot continues making requests
   - No re-login attempts

5. **Bulk Wildcard Queries**
   ```
   factory=*        → All factories
   machine=*        → All machines
   ```
   - Harvesting all data
   - Not typical user behavior

---

## Analysis Tips

### Step 1: Scan for Patterns
- Look for repeating timestamps
- Notice regular intervals
- Spot bulk queries

### Step 2: Compare Users
- How many requests per session?
- What's the time between requests?
- Do they load page resources?

### Step 3: Check Session Behavior
- Do they re-login when needed?
- How do they handle 401 errors?
- Do sessions span multiple days?

### Step 4: Identify Automation
- Perfect timing = bot
- No CSS/JS requests = script
- Simultaneous queries = automation
- Continued 401s = poor error handling

---

## Common Patterns

### Pattern 1: Morning Check
```
09:00 → Login
09:01 → Check factory X
09:02 → Check machine Y
09:03 → Logout/End
```
**Interpretation**: Employee checking status at work start

### Pattern 2: Investigation
```
14:30 → Login
14:31 → Check all factories
14:35 → Check factory X machines
14:38 → Check specific machine
14:40 → Check machine again
14:50 → End
```
**Interpretation**: Troubleshooting a problem

### Pattern 3: Automated Monitoring ⚠️
```
17:00:48 → Query all endpoints
18:00:48 → Query all endpoints
19:00:48 → Query all endpoints
```
**Interpretation**: BOT/SCRIPT (red flag!)

---

## Real Example: Suspicious User

```
192.168.0.101:
  2021-06-25T16:14:54.000Z POST "/login" 200
  2021-06-25T16:14:54.000Z GET  "/" {...} 200
  [... normal browsing ...]
  2021-06-25T17:00:48.000Z GET  "/api/factory/machine/status?factory=meiyo&machine=*" 200
  2021-06-25T17:00:48.000Z GET  "/api/factory/machine/status?factory=seiko&machine=*" 200
  2021-06-25T17:00:48.000Z GET  "/api/factory/machine/status?factory=shenzhen&machine=*" 200
  2021-06-25T17:00:48.000Z GET  "/api/factory/machine/status?factory=berlin&machine=*" 200
  2021-06-25T18:00:48.000Z GET  "/api/factory/machine/status?factory=meiyo&machine=*" 200
  2021-06-25T18:00:48.000Z GET  "/api/factory/machine/status?factory=seiko&machine=*" 200
  2021-06-25T18:00:48.000Z GET  "/api/factory/machine/status?factory=shenzhen&machine=*" 200
  2021-06-25T18:00:48.000Z GET  "/api/factory/machine/status?factory=berlin&machine=*" 200
```

### Red Flags:
1. ✅ Normal start (16:14)
2. ❌ At 17:00:48, behavior changes
3. ❌ 4 simultaneous queries (same timestamp)
4. ❌ Exact 1-hour intervals (17:00:48, 18:00:48)
5. ❌ Queries all factories with wildcards
6. ❌ No page resource requests
7. ❌ Perfect timing precision

**Conclusion**: Automated script deployed at 17:00

---

## Quick Checklist

When reviewing logs, ask:

- [ ] Are there exact time intervals?
- [ ] Are there simultaneous identical-timestamp requests?
- [ ] Do requests continue after 401 errors?
- [ ] Are there wildcard (*) queries?
- [ ] Is there bulk data harvesting?
- [ ] Are page resources (CSS/JS) loaded?
- [ ] Is timing humanly variable or machine-precise?
- [ ] Does user re-login when session expires?

**If you answer "yes" to the first 5 and "no" to the last 3, you've found a bot! 🤖**

---

## Practice Exercise

Look at this sequence and determine: Bot or Human?

```
10:30:15 POST /login 200
10:30:16 GET / {...} 200
10:30:45 GET /api/factory/status?factory=berlin 200
10:31:20 GET /api/factory/machine/status?factory=berlin&machine=CNC 200
10:35:10 GET /api/factory/machine/status?factory=berlin&machine=CNC 200
```

**Answer**: HUMAN
- Variable timing (30s, 35s, 4min)
- Specific queries (not wildcards)
- Logical flow (general → specific)
- Natural delays between actions

---

**Remember**: Humans are inconsistent, bots are precise! 🎯