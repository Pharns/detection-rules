# Detection Rules

[![Sigma Rules](https://img.shields.io/badge/Sigma-12%20Rules-blue)](https://github.com/SigmaHQ/sigma)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE%20ATT%26CK-Mapped-red)](https://attack.mitre.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **Built by Pharns Genece.** 12 Sigma rules, ATT&CK-mapped, developed and tuned in a homelab running Security Onion, Wazuh, and TheHive/Cortex. Related: [TraceLock](https://portfolio.pharns.com/cybersecurity/tracelock/) (RF detection engineering) · [portfolio](https://portfolio.pharns.com)

Custom Sigma detection rules developed and tuned in a homelab environment running Security Onion, Wazuh, and TheHive/Cortex.

## Overview

| Category | Rules | Focus Areas | MITRE ATT&CK |
|----------|-------|-------------|--------------|
| [DNS](rules/dns/) | 3 | Tunneling, DGA, suspicious TLDs | T1071.004 |
| [HTTP](rules/http/) | 3 | Beaconing, C2 callbacks, user-agents | T1071.001 |
| [Authentication](rules/authentication/) | 2 | Brute force, credential stuffing | T1110 |
| [Lateral Movement](rules/lateral-movement/) | 2 | SMB, PsExec, WMI abuse | T1021, T1047 |
| [Exfiltration](rules/exfiltration/) | 2 | Large transfers, encrypted channels | T1048 |

**Total: 12 detection rules**

## False Positive Optimization

These rules were tuned over a 3-month lab period to reduce false positives:

| Detection | Initial FP | After Tuning | Method |
|-----------|------------|--------------|--------|
| DNS tunneling | ~35% | ~12% | CDN allowlisting, long-label length threshold (entropy proxy) |
| HTTP beaconing | ~40% | ~18% | Candidate filtering, UA filtering (interval analysis requires SIEM correlation, not included) |
| Auth anomalies | ~25% | ~8% | Baseline normal hours per user group |
| Lateral movement | ~30% | ~15% | Admin workstation exclusions |

**Average FP reduction: ~20%**

## Lab Environment

Rules were developed and tested against:

- **SIEM:** Security Onion 2.4.x (Suricata, Zeek, Elasticsearch)
- **Host-Based:** Wazuh 4.x (endpoint logs, FIM)
- **Case Management:** TheHive 5.x + Cortex 3.x
- **Threat Intel:** MISP community feeds
- **Targets:** Windows 11, Active Directory, Ubuntu/Docker, DVWA, Juice Shop

## Usage

### With Security Onion / Elasticsearch

```bash
# Clone the repo
git clone https://github.com/Pharns/detection-rules.git

# Convert Sigma to Elasticsearch query
sigma convert -t elasticsearch rules/dns/dns_tunneling_entropy.yml
```

### With Splunk

```bash
sigma convert -t splunk rules/dns/dns_tunneling_entropy.yml
```

### With Microsoft Sentinel

```bash
sigma convert -t azure-monitor rules/dns/dns_tunneling_entropy.yml
```

## Rule Structure

Each rule follows the [Sigma specification](https://github.com/SigmaHQ/sigma/wiki/Specification):

```yaml
title: Descriptive title
status: experimental | test | stable
description: What the rule detects and why
author: Pharns Genece
date: YYYY/MM/DD
references:
  - https://attack.mitre.org/techniques/TXXXX/
logsource:
  product: zeek | windows | ...
  service: dns | security | ...
detection:
  selection:
    field|modifier: value
  condition: selection
falsepositives:
  - Known benign scenarios
level: low | medium | high | critical
tags:
  - attack.tactic
  - attack.tXXXX
```

## Directory Structure

```
detection-rules/
├── README.md
├── LICENSE
├── rules/
│   ├── dns/
│   │   ├── dns_tunneling_entropy.yml
│   │   ├── dns_dga_detection.yml
│   │   └── dns_suspicious_tld.yml
│   ├── http/
│   │   ├── http_beaconing_pattern.yml
│   │   ├── http_c2_callback.yml
│   │   └── http_suspicious_user_agent.yml
│   ├── authentication/
│   │   ├── auth_brute_force.yml
│   │   └── auth_anomalous_login_time.yml
│   ├── lateral-movement/
│   │   ├── lateral_smb_enumeration.yml
│   │   └── lateral_psexec_wmi.yml
│   └── exfiltration/
│       ├── exfil_large_outbound.yml
│       └── exfil_encrypted_channel.yml
└── .github/
    └── workflows/
        └── validate.yml
```

## Contributing

Contributions welcome. Please:

1. Follow the Sigma specification
2. Include MITRE ATT&CK mapping
3. Document false positive scenarios
4. Test in a lab environment before submitting

## Related Projects

- [Portfolio - Detection Engineering](https://portfolio.pharns.com/cybersecurity/detection-engineering/) — Full detection lab documentation
- [TraceLock](https://portfolio.pharns.com/cybersecurity/tracelock/) — RF/wireless detection engineering

## Author

**Pharns Genece**
GRC Engineer | Detection Engineering | Cloud Security

- [Portfolio](https://portfolio.pharns.com)
- [LinkedIn](https://linkedin.com/in/pharns)
- [GitHub](https://github.com/Pharns)

## License

MIT License - See [LICENSE](LICENSE) for details.
