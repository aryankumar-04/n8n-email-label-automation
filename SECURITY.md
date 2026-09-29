# Security Policy

## Reporting Security Issues

Security is the top priority for this project. If you discover a vulnerability or sensitive data leakage, please report it responsibly.

### How to Report
Please **DO NOT** open a public issue on GitHub for security vulnerabilities.
Instead, use GitHub's private vulnerability reporting feature:
- Navigate to the repository's **Security** tab.
- Click **Report a vulnerability** to open an encrypted advisory draft.

Alternatively, contact the repository maintainer through their GitHub profile: [@aryankumar-04](https://github.com/aryankumar-04).

## Critical Security Guidelines for Users
- **Never Commit Secrets**: Never commit real credentials, `.env` files, API keys, OAuth client secrets, or private tokens to public or private Git repositories.
- **Workflow Sanitization**: The workflow export in this repository (`workflow/Email-Lable-Automation.json`) is fully sanitized. When re-exporting workflows from your personal n8n instance, always audit for OAuth tokens, credential IDs, and static data caches before sharing.
- **Credential Scope Limitation**: Grant only the minimal scopes required (`https://www.googleapis.com/auth/gmail.modify`). Avoid broader scopes like full account access.
