# Flipper Purple Team

BadUSB and HID attack payloads for Flipper Zero paired with their exact SIGMA detection rules, KQL hunting queries, and forensic artifacts - tested end-to-end so you can run the attack and validate your detections close the gap.

Most BadUSB payload collections exist in isolation from any detection content. Most HID attack detection rules are generic enough to miss the specific traces that Flipper Zero leaves. This repo connects both sides of the table.

---

## How this works

Every entry in this repo has four components:

1. **Payload** - a Flipper Zero BadUSB script (`.txt` format, Rubber Ducky syntax)
2. **SIGMA rule** - vendor-neutral detection contributed upstream to SigmaHQ
3. **KQL query** - Microsoft Sentinel hunting query contributed to Azure-Sentinel
4. **Forensic breakdown** - exactly which events fire, on which log sources, with what field values

Run the payload on a test machine. Check your SIEM. If the rule fires, your detection is working. If it does not, now you know the gap.

---

## Coverage

### BadUSB / HID Injection

| # | Attack | OS | SIGMA | KQL | Elastic | Status |
|---|--------|----|-------|-----|---------|--------|
| 01 | [PowerShell execution via Run dialog](badusb/01-windows-powershell-run-dialog/) | Windows | Yes | Yes | Planned | Done |
| 02 | [Encoded command download cradle](badusb/02-windows-encoded-download-cradle/) | Windows | Yes | Planned | Planned | Done |
| 03 | [macOS terminal command execution](badusb/03-macos-terminal-execution/) | macOS | Yes | - | Planned | Done |
| 04 | [Windows LOLBIN execution (certutil)](badusb/04-windows-lolbin-certutil/) | Windows | Yes | Yes | - | Done |
| 05 | Linux bash reverse shell (not started) | Linux | Planned | - | Planned | Planned |

### Sub-GHz / RF

Detection of Sub-GHz replay attacks is largely outside SIEM scope. See [sub-ghz/README.md](sub-ghz/README.md) for physical security log correlation guidance.

### NFC / RFID

NFC cloning leaves traces only in physical access control systems. See [nfc/README.md](nfc/README.md).

---

## Requirements

**To run payloads:**
- Flipper Zero with BadUSB app (stock firmware)
- A test machine you own or have written authorization to test

**To validate detections:**
- Microsoft Sentinel (KQL queries) or any SIEM with SIGMA support
- Sysmon deployed on Windows test machines (config: [SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) recommended)
- PowerShell Script Block Logging enabled (Event ID 4104)

**This repo does not:**
- Provide payloads targeting specific organizations
- Include persistence mechanisms intended for long-term access
- Contain C2 infrastructure

---

## Methodology

The detection side of every entry starts from the forensic artifacts, not from the payload source. For each attack:

1. Run the payload on a clean test machine
2. Collect all generated events (Sysmon, Windows Security, PowerShell operational, USB events)
3. Identify the minimum set of fields that distinguish this attack from legitimate activity
4. Write the rule against those fields
5. Tune for false positives against a 30-day baseline of normal keyboard and process activity

The goal is rules that fire on the exact attack, not rules that fire on everything and need analyst discretion to be useful.

---

## Contributing

Contributions follow the same format: payload + SIGMA rule + KQL query + forensic breakdown. See [CONTRIBUTING.md](CONTRIBUTING.md).

SIGMA rules from this repo are contributed upstream to [SigmaHQ/sigma](https://github.com/SigmaHQ/sigma). KQL queries go to [Azure/Azure-Sentinel](https://github.com/Azure/Azure-Sentinel).

---

## Author

**descambiado** - SOC Analyst and Detection Engineer. Purple team work across Microsoft Sentinel, Elastic SIEM, Splunk, SigmaHQ, and hardware security tooling.

[github.com/descambiado](https://github.com/descambiado)
