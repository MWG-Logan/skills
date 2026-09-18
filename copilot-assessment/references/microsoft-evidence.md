# Optional Microsoft evidence fallback

This reference is **manual and read-only**. Do not run it automatically from the skill. Prefer Hudu and CIPP evidence; use these paths only when an authorized technician has an approved Microsoft evidence path and the required permissions. Aggregate or redact results before placing them in the report.

## Official Microsoft Learn sources

| Evidence | Official source |
|---|---|
| Copilot readiness report | https://learn.microsoft.com/en-us/microsoft-365/admin/activity-reports/microsoft-365-copilot-readiness?view=o365-worldwide |
| App and network requirements | https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-requirements |
| Setup and update-channel guidance | https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-setup and https://learn.microsoft.com/en-us/microsoft-365-apps/updates/change-channel-for-copilot |
| Data and compliance readiness | https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-minimum-requirements-data-compliance |
| Data, privacy, and security | https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-privacy |
| Entra authentication-method registration | https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-methods-activity |
| Graph MFA registration detail cmdlet | https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.reports/get-mgreportauthenticationmethoduserregistrationdetail?view=graph-powershell-1.0 |
| Graph Office activation detail/count cmdlets | https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.reports/get-mgreportoffice365activationuserdetail?view=graph-powershell-1.0 and https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.reports/get-mgreportoffice365activationusercount?view=graph-powershell-1.0 |
| Graph Office activation API | https://learn.microsoft.com/en-us/graph/api/reportroot-getoffice365activationsuserdetail?view=graph-rest-1.0 |
| Intune policy monitoring | https://learn.microsoft.com/en-us/intune/device-configuration/monitor-device-profile |
| Intune compliance monitoring | https://learn.microsoft.com/en-us/intune/device-security/compliance/monitor-policy |

## Small manual PowerShell reference

```powershell
# OPTIONAL / MANUAL ONLY. Read-only Graph reports; never run automatically.
Import-Module Microsoft.Graph.Reports

# Aggregate MFA registration evidence; discard identifiers before reporting.
Get-MgReportAuthenticationMethodUserRegistrationDetail -All |
    Select-Object IsMfaCapable, IsMfaRegistered, IsSsprEnabled |
    Group-Object IsMfaCapable

# Aggregate Office activation evidence; keep the downloaded CSV restricted and
# summarize counts before recording it in the assessment.
Get-MgReportOffice365ActivationUserCount -OutFile .\office-activation-user-count.csv
```

These commands require the appropriate Microsoft Graph permissions and an authorized session. They do not establish effective Office update-channel state; do not use them to override the CIPP exclusion rule.
