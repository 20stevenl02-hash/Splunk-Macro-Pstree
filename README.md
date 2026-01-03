# Splunk-Macro-Pstree

## Overview

Splunkbase provides a **PSTree** app that generates a process tree view for a given host. However, this app is **only available for Splunk Enterprise** and is **not supported in Splunk Cloud**.

To address this limitation, this repository provides **two custom Splunk macros** that replicate PSTree-style functionality using native Windows logs. These macros are designed to work in **Splunk Cloud** and **Splunk Enterprise** environments.

Each macro builds a parent/child process tree for a specified host using different Windows data sources:

* **Sysmon Event ID 1 (Process Create)**
* **Windows Security Event ID 4688 (Process Creation)**

Both macros accept **one argument: the hostname**.

---

## Macros Included

### 1. PSTree via Sysmon (Event ID 1)

**Data Source:** Sysmon

**Event Used:**

* `EventID=1` – Process creation

**Purpose:** Creates a hierarchical process tree using Sysmon process creation events, including parent/child relationships derived from `ParentProcessId` and `ProcessId`.

**Example Usage:**

```spl
`pstree(host=<my-hostname>)`
```

---

### 2. PSTree via Windows Security Logs (Event ID 4688)

**Data Source:** Windows Security Event Log

**Event Used:**

* `EventCode=4688` – A new process has been created

**Purpose:** Builds a process tree using Windows Security process creation events. This is useful in environments where Sysmon is not deployed.

**Example Usage:**

```spl
`pstree4688(host=<my-hostname>)`
```

---

## Macro Arguments

Both macros accept **exactly one argument**:

| Argument   | Description                                                    |
| ---------- | -------------------------------------------------------------- |
| `host` | The target host for which the process tree should be generated |

---

## Sourcetype Considerations (Important)

⚠️ **Environment-Specific Configuration Required**

The **sourcetypes referenced inside each macro may need to be modified** to match your environment.

Different Splunk deployments often use different sourcetypes depending on:

* Windows TA configuration
* Custom inputs.conf settings
* Data normalization practices

### Examples

You may need to adjust values such as:

* `sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational`
* `sourcetype=WinEventLog:Security`

If your environment uses custom or renamed sourcetypes, update the macro definitions accordingly.

---

## Requirements

* Splunk Cloud or Splunk Enterprise
* One or more of the following:

  * Sysmon deployed and logging Event ID 1
  * Windows Security auditing enabled for Event ID 4688
* Indexed Windows event data

---

## Why Use These Macros?

* ✅ Works in **Splunk Cloud**
* ✅ No third-party apps required
* ✅ Lightweight and customizable
* ✅ Uses native Windows telemetry

---

## Notes

* These macros are intended for **investigation and threat hunting**, similar to traditional PSTree visualizations.
* Output structure and depth depend on log completeness and retention.
* For best results, ensure consistent logging across hosts.

---

## License

Use and modify freely for internal security operations, detection engineering, and threat hunting use cases.

