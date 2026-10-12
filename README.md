# Cedar Bridge Cybersecurity Case Study

A fictional SOC analyst case study covering asset risk assessment, authentication log analysis, network security fundamentals, access control, and incident reporting.

## Project Overview

Cedar Bridge is a fictional organization created for this educational cybersecurity exercise.

The case involves suspicious authentication activity associated with a fictional employee account. Three failed login attempts are followed by a successful login from an unfamiliar source. A subsequent attempt to download a restricted payroll file is denied.

The objective is to review the supplied evidence, identify security concerns, distinguish observations from assumptions, and recommend appropriate investigative steps.

**Case type:** Fictional educational scenario  
**Evidence source:** Supplied lab records  
**Investigation status:** Preliminary analysis  
**Real-world incident:** None

## Learning Objectives

- Identify information assets and assess potential risks.
- Apply confidentiality, integrity, and availability concepts.
- Distinguish authentication from authorization.
- Interpret failed and successful login events.
- Explain DNS, HTTPS, NAT, and firewall decisions.
- Interpret basic Linux terminal output and file permissions.
- Document findings and evidence gaps.
- Recommend appropriate incident investigation steps.

## Repository Contents

- [Case Overview](case-overview/case-summary.md)
- [Asset Risk Assessment](asset-risk-assessment/asset-register.md)
- [Network Connection Analysis](network-analysis/network-connection.md)
- [Network Diagram](network-analysis/diagrams/Cedar-Bridge-Network-Diagram.png)
- [Authentication Log Analysis](log-analysis/authentication-log-analysis.md)
- [Initial Incident Report](incident-report/initial-incident-report.md)
- [Week 1 Analyst Workbook](analyst-workbook/week-1-workbook.md)
- [Disclaimer](DISCLAIMER.md)

## Key Finding

The supplied records show three failed login attempts followed by a successful login from an unfamiliar source. A subsequent payroll download attempt was denied by role-based access control.

This sequence warrants further investigation, but the evidence does not prove account compromise or payroll data disclosure.

## Evidence-Based Analysis

The project distinguishes recorded events from possible explanations. An unfamiliar IP address, failed logins, or a successful login does not independently establish malicious activity.

The analysis identifies additional evidence needed to strengthen the assessment.

## Limitations

The project uses supplied fictional records. No live network investigation, endpoint investigation, or real-world incident response was performed.

The conclusions are limited to the evidence described in this repository.

## Disclaimer

This repository is for educational purposes and cybersecurity portfolio development. It does not represent a real client engagement, a confirmed breach, or proof of professional incident-response experience.
