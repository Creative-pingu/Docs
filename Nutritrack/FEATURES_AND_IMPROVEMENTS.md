# NutriTrack - Features and Improvements

> **Last Updated**: 2026-08-16
> **Purpose**: Action items and bug tracking for discussion with Architect Chat
> **Owner**: Nick
> **Status**: Review steps, privacy focus, and decision-making process order IMPLEMENTED. CRITICAL BUGS IDENTIFIED - BLOCKING.

---

## CRITICAL SECURITY BUGS (IMMEDIATE ACTION REQUIRED)

These are blocking issues that violate core privacy principles and must be fixed before any further development.

### S1: Hardcoded External API Call (PRIVACY VIOLATION)
ID: S1
Severity: CRITICAL
Status: OPEN - NOT FIXED
File: NutriTrack.jsx
Function: parseRecipesFromPasteText()

Issue: Makes direct POST requests to Anthropic API with user pasted recipe text, transmitting user data externally without explicit consent.

Violations:
- PROJECT_CHARTER: No user data collected or transmitted without explicit action
- PROJECT_CHARTER: Local-Only Data Storage
- PROJECT_CHARTER: Privacy by Design

Impact: Data leakage, privacy violation, potential compliance issues.

Recommended Fix: Remove function entirely, replace with local-only parsing.

Owner: Dev Chat -> Architect Chat
Blocks: All phases until resolved
ETC: 1-2 hours

---

### S2: eval() Usage (XSS VULNERABILITY)
ID: S2
Severity: CRITICAL
Status: OPEN - NOT FIXED
File: index.html
Function: loadApp()

Issue: Uses eval to execute Babel-transformed code fetched from network. Creates remote code execution vulnerability.

Impact: Remote code execution, complete app compromise.

Recommended Fix: Pre-compile JSX to JS during build/deploy.

Owner: Dev Chat -> Architect Chat
Blocks: All phases until resolved
ETC: 2-4 hours