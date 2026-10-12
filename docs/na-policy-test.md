# N/A policy test (2026-10-13)

Question: do the 16 CIS rules marked not applicable on RHEL really fail on the browsers we run, or do some still
work on certain versions (then a version gate would be needed)?

## Setup

- VM: rhel9 (192.168.20.30), both browsers installed by hand:
  `google-chrome-stable-155.0.8059.39-1.x86_64`, `chromium-154.0.8037.92-1.el9.x86_64`
- Role branch `version-policy-rule` (discovery: one run writes both browsers' policy files).
- `sysconfig/host_vars/rhel9.yml`: the 16 rules set to `true` (temporary), plus
  `chrome_cis_third_party_storage_blocked_origins: ["https://example.com"]` so 2.3.5 is written.
- Run: `ansible-playbook playbooks/site.yml --limit rhel9 --tags not_applicable` → `runs/rhel9-na-test.log`:
  `policies_written: 16`, both folders, `failed=0`.
- Check: `chrome://policy` → Reload policies, in each browser.

## Result

| Rule | Policy | Why N/A | Chrome 155 | Chromium 154 |
|---|---|---|---|---|
| 1.10 | `CertificateTransparencyEnforcementDisabledForLegacyCas` | removed from Chrome | Error | Error |
| 1.19 | `ThirdPartyBlockingEnabled` | removed (was Windows only) | Error | Error |
| 2.1.1 | `Update{8A69D345-D564-463C-AFF1-A69D9E530F96}` | Google Update setting, not Chrome | Error | Error |
| 2.1.2 | `AutoUpdateCheckPeriodMinutes` | Google Update setting, not Chrome | Error | Error |
| 2.3.4 | `DefaultThirdPartyStoragePartitioningSetting` | removed from Chrome | Error | Error |
| 2.3.5 | `ThirdPartyStoragePartitioningBlockedForOrigins` | removed from Chrome | Error | Error |
| 2.3.6 | `ExtensionManifestV2Availability` | removed from Chrome | Error | Error |
| 2.7.1 | `CloudPrintProxyEnabled` | removed from Chrome | Error | Error |
| 2.8.2 | `RemoteAccessHostAllowUiAccessForRemoteAssistance` | Windows only | Error | Error |
| 2.9.1 | `FirstPartySetsEnabled` | removed from Chrome | Error | Error |
| 2.10.1 | `CloudAPAuthEnabled` | Windows only | Error | Error |
| 2.23 | `EnforceLocalAnchorConstraintsEnabled` | removed from Chrome | Error | Error |
| 2.29 | `InsecureHashesInTLSHandshakesEnabled` | removed from Chrome | Error | Error |
| 2.30 | `RendererAppContainerEnabled` | Windows only | Error | Error |
| 3.6 | `ChromeCleanupReportingEnabled` | removed (was Windows only) | Error | Error |
| 3.13 | `SafeBrowsingForTrustedSourcesEnabled` | Windows only | Error | Error |

All 16: Error in both browsers. Chrome and Chromium behave the same.

Evidence: `evidence/01-rhel9-na-chrome155-all-error.png`, `evidence/02-rhel9-na-chromium154-all-error.png`,
`runs/rhel9-na-test.log`.

## Earlier results

Chrome 144 (rhel9) and Chrome 155: the 14 N/A policies (all but 2.1.1, 2.1.2) showed Error.

## Conclusion

<!-- fill in after the decision -->
