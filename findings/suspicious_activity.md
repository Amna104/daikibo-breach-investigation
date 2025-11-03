# Suspicious Activity - Quick Reference

## 🚨 Primary Suspect

**User ID**: `mdB7yD2dp1BFZPontHBQ1Z`  
**IP Address**: `192.168.0.101`  
**Risk Level**: 🔴 CRITICAL

---

## Key Evidence

### 1. Automated Polling Pattern

**Time Pattern**:
```
17:00:48
18:00:48
19:00:48
20:00:48
21:00:48
22:00:48
23:00:48
```
- **Interval**: Exactly 3600 seconds (1 hour)
- **Precision**: Down to the second (.48 seconds)
- **Conclusion**: Automated script, not human

### 2. Simultaneous Bulk Queries

At each interval, requests to ALL factories simultaneously:
```
/api/factory/machine/status?factory=meiyo&machine=*
/api/factory/machine/status?factory=seiko&machine=*
/api/factory/machine/status?factory=shenzhen&machine=*
/api/factory/machine/status?factory=berlin&machine=*
```

### 3. Post-Expiration Behavior

After session expired (00:00:48), bot continued making requests:
- 16 consecutive 401 errors
- No re-authentication attempts
- Indicates poor bot design/lack of error handling

---

## Comparison: Normal vs. Malicious

### Normal User Behavior
- ✅ Variable timing (30s to 5 minutes between requests)
- ✅ Specific, targeted queries
- ✅ Proper session management
- ✅ Interactive navigation patterns

### Malicious User Behavior
- ❌ Exact time intervals
- ❌ Bulk queries to all endpoints
- ❌ Ignores session expiration
- ❌ No interactive patterns

---

## Attack Timeline

```
16:14:54 → Login (normal)
16:15-16:59 → Manual reconnaissance
17:00:48 → Bot activated (start of hourly polling)
17:00-23:00 → Data exfiltration (7 successful cycles)
00:00:48 → Session expires (bot continues blindly)
00:00-16:00 → 16 hours of failed attempts
16:04:54 → Re-authentication
17:00:48 → Bot resumes
```

---

## Red Flags Checklist

- [x] Exact time intervals between requests
- [x] Simultaneous multi-endpoint queries
- [x] Continued activity after 401 errors
- [x] No page resource requests (CSS/JS)
- [x] Perfect timing precision
- [x] Bulk data queries
- [x] Extended session duration (8+ hours)
- [x] No human variation in behavior

---

## Other Users Analyzed

All other users showed normal behavior patterns:
- Random request intervals
- Targeted queries
- Proper session management
- Human-like navigation

**Conclusion**: Only `mdB7yD2dp1BFZPontHBQ1Z` exhibits malicious patterns.

---

## Investigation Answers

### Q1: Could breach occur from internet directly?
**Answer**: ❌ NO
- All IPs are internal (192.168.0.x)
- Attacker had network access (VPN or insider)

### Q2: Suspicious user ID?
**Answer**: ✅ `mdB7yD2dp1BFZPontHBQ1Z`
- Automated polling at exact intervals
- Bulk data exfiltration
- Bot-like behavior

---

**Status**: Investigation Complete  
**Action Required**: Disable account immediately