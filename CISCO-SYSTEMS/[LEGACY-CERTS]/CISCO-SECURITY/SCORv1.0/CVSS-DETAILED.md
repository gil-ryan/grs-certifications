# SCOR

## Security Core Exam

## CVSS - Common Vulnerability Scoring System

CVSS was developed as a cooperative effort between the National Infrastructure Advisory Council and several security industry vendors and research organizations, including Cisco.

The Forum of Incident Response and Security Teams (FIRST) has been designated as the custodian of CVSS to promote its adoption globally.

## CVSS v3.0 Base Metrics

The base metric group represents the intrinsic characteristics of a vulnerability that are constant over time and across user environments. 

* It is composed of two sets of metrics: 
    + the exploitability metrics (attack vector, attack complexity, privileges required, user interaction, scope) 
    +  the impact metrics (confidentiality, integrity, availability).

### Attack Vector (AV)

 This metric reflects the context by which vulnerability exploitation is possible. This metric value and the base score will correlate with an attacker's proximity to a vulnerable component. The score will be higher the more remote (logically and physically) an attacker is from the vulnerable component.

*  Local: Exploiting the vulnerability requires either physical access to the target or a local (shell) account on the target.

* Adjacent: Exploiting the vulnerability requires access to the local network of the target and cannot be performed across an OSI Layer 3 boundary.

* Network: The vulnerability is exploitable from remote networks. Such a vulnerability is often termed "remotely exploitable," and can be thought of as an attack being exploitable one or more network hops away, such as across Layer 3 boundaries from routers.

* Physical: A vulnerability exploitable with physical access requires the attacker to physically touch and manipulate the vulnerable component.

### Attack Complexity (AC)

This metric describes the conditions beyond the attacker's control that must exist in order to exploit the vuln.

* None: The attacker is unauthorized before attack, and therefore does not require any acccess to setting or files to carry it out
* Low: The attacker is authorized with privileged that provide basic user capabilities that could normally affect only settings and files that are owned by a user. Alternatively, an attacker with lowe privileged may have that ability to cause an impact only to non-sensitive resources.
* High: The attacker is authorized with privileges that provide significant (e.g., adminstrative) control over the vulnerable component that could affect component-wide settings and files.

### Privileges Required (PR)

* None
* Low
* High

### User Interface (UI)

* None
* Required

### Scope (S)

* Unchanged
* Changed

### Confidentiality Impact (C)

* None
* Low
* High

### Availability Impact (A)

* None
* Low
* High

## CVSS v3.0 Temporal Metrics

Temporal metrics measure the current state of exploit techniques or code availability. Also - the existence of any patches or workarounds.

### Exploit Code Maturity (E)

* Not Defined
* Unproven
* Proof-of-concept
* Functional
* High

### Remediation Level (RL)

* Not defined
* Unavailable
* Workaround
* Temporary fix
* Official fix

### Report Confidence

* Not defined
* Unknown
* Reasonable
* Confirmed

## CVSS v3.0 Enviromental Metrics

### Security Requirements (CR, IR, AR)

* Not defined
* Low
* Medium 
* High

### Modified Base Metrics:

* These metrics are adjustable:

    - Modified Attack Vector (MAV)
    -  Modified Attack Complexity (MAC)
    - Modified Privileges Required (MPR)
    - Modified User Interaction (MUI)
    - Modified Scope (MS)
    - Modified Confidentiality (MC)
    - Modified Integrity (MI)
    - Modified Availability (MA)

## CVSS v3.0 Calculation

![https://www.first.org/cvss/calculator/3.0#CVSS:3.0/AV:N/AC:L/PR:H/UI:R/S:U/C:H/I:H/A:L/E:F/RL:U/RC:R/CR:M/IR:L/AR:L/MAV:A/MAC:L/MPR:N/MUI:N/MS:U/MC:L/MI:L/MA:L](https://www.first.org/cvss/calculator/3.0#CVSS:3.0/AV:N/AC:L/PR:H/UI:R/S:U/C:H/I:H/A:L/E:F/RL:U/RC:R/CR:M/IR:L/AR:L/MAV:A/MAC:L/MPR:N/MUI:N/MS:U/MC:L/MI:L/MA:L)
