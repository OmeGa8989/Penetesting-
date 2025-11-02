# Pacu EC2 Penetration Testing and IAM policy

## Overview

This repository contains a concise, auditable test plan and a recommended IAM policy to support **approved, limited Pacu testing** against a single EC2 test instance. The goal is to enable safe enumeration and basic non-destructive testing while following the principle of least privilege.

> ⚠️ **Important:** Only perform tests after obtaining written authorization that explicitly names the AWS Account ID and EC2 Instance ID, and clearly states the allowed scope and time window.

---

## Contents

* `README.md` — this file
* `policies/` — suggested IAM policy JSON files

  * `ec2-enumeration.json` — Overpriveleged EC2 + IAM list/get policy (recommended default)
  * `ec2-single-instance-control.json` — scoped policy for control over a single instance (only if explicitly approved)
* `test-plan.md` — step-by-step safe test plan and documentation template

---

---

## Test Plan (high level)

This is a short, auditable plan you can include when requesting or recording formal approval.

1. **Objective:** Perform read-only enumeration of a single EC2 test instance using Pacu to collect configuration and identify obvious misconfigurations.
2. **Scope:** AWS Account `{{ACCOUNT_ID}}`, Region `{{REGION}}`, EC2 Instance `{{INSTANCE_ID}}` — only the resources listed in the approval.
3. **Allowed Actions:** Only `Describe*` EC2 and `Get/List` IAM actions (see `ec2-readonly-enumeration.json`).
4. **Disallowed Actions:** Any `Put*`, `AttachRolePolicy`, `Create*`, `TerminateInstances`, `RunInstances`, or privilege escalation modules without additional authorization.
5. **Test Window:** `YYYY-MM-DD HH:MM` to `YYYY-MM-DD HH:MM` (UTC)
6. **Logging & Monitoring:** CloudTrail must be enabled. Record all Pacu commands and output. Provide exported session JSON and a short findings report.
7. **Cleanup:** Revoke keys used for testing and delete the Pacu session entry (example: `delete_session <session_name>`).

---

## Example Safe Pacu Workflow

1. Start Pacu (preferably inside an isolated virtualenv or WSL):

```bash
python -m pacu
```

2. Load test keys (the set must be from the approved IAM user):

```
set_keys
```

3. Confirm identity and session:

```
whoami
sessions/list_sessions
```

4. Run enumeration modules only:

```
run enum_ec2
run enum_iam
run ec2__download_userdata
```

5. Export/save results (take screenshots or copy module outputs). Then exit:

```
exit
```

6. Delete the saved Pacu session (if requested by policy):

```
delete_session <session_name>
```

---

## Safety & Compliance Checklist

* [ ] Written authorization attached to this repository (redact secrets)
* [ ] CloudTrail enabled for the AWS account
* [ ] Test credentials created with least privilege
* [ ] Testing scheduled and communicated to stakeholders
* [ ] Output and findings stored securely
* [ ] Test keys revoked after testing

---

## How to use this repo

1. Replace placeholders (`<region>`, `<account-id>`, `<instance-id>`, `{{ACCOUNT_ID}}`, etc.) with real values in a protected environment (do not commit secrets).
2. Attach the appropriate JSON policy via the AWS Console or `aws iam put-role-policy` commands.
3. Keep this repository as documentation for approvals and scope.

---

## Disclaimer

This repository is for **authorized** testing only. Unauthorized scanning, exploitation, or the use of Pacu against systems you do not own or have permission to test is illegal and unethical. Always obtain explicit written authorization and operate within your organization’s policies.

---

## License

MIT License — see LICENSE file.

---

## Contributors

* `Adhyan Aditya <adhyanaditya88@gmail.com>`
