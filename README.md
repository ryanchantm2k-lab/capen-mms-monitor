# Capen MMS cloud monitor

A small public GitHub Actions scheduler checks whether the Capen MMS background
helper is healthy. It contains only this README and the generic workflow. It
does not contain the application, student records, booking data, passwords,
webhook URLs or MMS credentials.

The workflow requests a short-lived GitHub OIDC identity and calls the public
monitor endpoint. The application independently verifies the issuer, audience,
repository name and immutable ID, workflow path, branch, event and expiry.
The application checks its own stored helper heartbeat and sends generic
attention messages to its existing private staff destination when required.
No student data is returned to this workflow.

## Activation

The scheduled job is off until this repository's Actions variable
`MMS_MONITOR_ENABLED` is set to `true`. Configure the application's trusted
`MMS_MONITOR_REPOSITORY` and `MMS_MONITOR_REPOSITORY_ID` first, deploy the monitor
endpoint, and verify a manual workflow dispatch before enabling the schedule.
These repository identifiers are not secrets. No additional GitHub secret is
required. Never add application credentials to this repository.

For an authorised connection check, manually run the workflow with `test_alert`
set to `true`. The server accepts this option only from a verified manual
dispatch and sends a fixed, clearly labelled TEST ONLY message to the existing
private staff destination. It changes no helper health or booking records.
Tests are limited to one attempt per hour. Unknown delivery results stay held
for review rather than being sent again, even after the hour expires. The
workflow prints only the acknowledged message ID, never a webhook or token.
Leave `test_alert` false for ordinary checks and all scheduled runs.

The five-minute schedule runs independently of the office Mac. GitHub can delay
or drop scheduled runs; this is best-effort monitoring, not an availability SLA.
The Mac still needs power, a signed-in user, internet and valid application
sign-ins to process work. Monitoring does not run or repair MMS bookings.

Standard GitHub-hosted runner usage in a public repository is free under
[GitHub's documented Actions billing rules](https://docs.github.com/en/billing/concepts/product-billing/github-actions).
The scheduler keeps logs to generic status and issue counts. It does not print
the OIDC token. Do not move this schedule into a private repository without
checking its Actions allowance and billing.

## Recovery and disabling

Set `MMS_MONITOR_ENABLED` to `false` or disable the workflow to stop scheduled
checks. A manual dispatch remains possible. The server deduplicates incidents;
an acknowledged alert is not posted repeatedly. An unknown delivery result is
held for review rather than automatically risking a duplicate message.

References: [GitHub OIDC](https://docs.github.com/en/actions/reference/security/oidc),
[schedule limitations](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule).
