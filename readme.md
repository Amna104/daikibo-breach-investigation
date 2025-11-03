# Daikibo Industrials - Cyber Security Breach Investigation

![Security](https://img.shields.io/badge/Security-Investigation-red)
![Status](https://img.shields.io/badge/Status-Completed-green)

## 📋 Project Overview

This repository contains the investigation of a suspected cyber security breach at Daikibo Industrials. A major news publication revealed sensitive private information causing production problems and supply chain disruptions.

### Background
- **Client**: Daikibo Industrials
- **Incident**: Unauthorized access to production status board
- **Impact**: Production line stoppage, sensitive data leak
- **Investigation Period**: June 25-29, 2025

## 🎯 Investigation Objectives

1. **Determine attack vector**: Could the breach have occurred from the internet directly (without VPN access)?
2. **Analyze web logs**: Identify suspicious user activity and potential automated attacks
3. **Identify threat actor**: Determine which user account was compromised

## 📁 Repository Structure

```
├── logs/                       # Original log files
│   └── web_activity.log       # Web request logs from the incident period
├── analysis/                   # Investigation reports
│   └── breach_analysis_report.md
├── findings/                   # Detailed findings
│   └── suspicious_activity.md
└── resources/                  # Reference materials
    └── log_reading_guide.md
```

## 🔍 Key Findings

### Suspicious User Identified
- **User ID**: `mdB7yD2dp1BFZPontHBQ1Z`
- **IP Address**: `192.168.0.101`
- **Attack Pattern**: Automated polling at exact hourly intervals

### Attack Characteristics
- Automated API requests every hour (on the 48th second)
- Simultaneous queries to all factories
- Continued after session expiration (401 errors)
- Pattern indicates bot/script usage

## 🛠️ Methodology

1. **Log Analysis**: Examined web_activity.log for unusual patterns
2. **Timeline Construction**: Mapped user sessions chronologically
3. **Pattern Recognition**: Identified automated vs. manual behavior
4. **Correlation Analysis**: Cross-referenced suspicious activity with breach timeline

## 📊 Evidence

### Normal User Behavior
- Manual login sequences
- Random intervals between requests
- Targeted factory/machine queries
- Session management (re-login when needed)

### Malicious Activity
- Exact hourly intervals (17:00:48, 18:00:48, etc.)
- No re-authentication after session expiry
- Bulk queries to all factories simultaneously
- Continued attempts despite 401 errors

## 🚨 Security Recommendations

1. **Implement rate limiting** on API endpoints
2. **Add CAPTCHA** for repeated login failures
3. **Monitor for automated access patterns**
4. **Implement IP blocking** for suspicious behavior
5. **Add multi-factor authentication** (MFA)
6. **Review and rotate** all user credentials
7. **Audit VPN access logs**

## 📝 Technical Details

### Log File Structure
- **Format**: Grouped by IP address
- **Time Range**: June 25-29, 2025
- **Network**: Internal Daikibo network (192.168.0.x)
- **Authentication**: Session-based with user IDs

### API Endpoints Monitored
- `/api/factory/status?factory=*` - All factories status
- `/api/factory/machine/status?factory={name}&machine=*` - Factory machines
- `/api/factory/machine/status?factory={name}&machine={type}` - Specific machines

## 🔒 Breach Assessment

**Could the attack come directly from the internet?**
- **Answer**: NO
- **Reasoning**: All IP addresses are internal (192.168.0.x range)
- **Conclusion**: Attacker had VPN access or was internal

## 📚 Skills Demonstrated

- Log file analysis
- Pattern recognition in security logs
- Cyber threat investigation
- Web application security
- Incident response

## 🎓 Learning Outcomes

- Understanding web request logs
- Identifying automated vs. manual access patterns
- Recognizing security breach indicators
- Supporting clients during security incidents

## 📄 License

This is a cybersecurity simulation project for educational purposes.

## 👤 Author

Created as part of a cybersecurity virtual simulation training program.

---

**⚠️ Disclaimer**: This is an educational project based on a cybersecurity simulation. All data is fictional and used for learning purposes only.