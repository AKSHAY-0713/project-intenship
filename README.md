# Threat Model and Secure Configuration

**Project:** Small Web Application Threat Model  
**Method:** STRIDE  
**Version:** 1.0

## Scope and assumptions
This assessment models a small web application with user registration/login, profile viewing/editing, and a database. Users access the app over HTTPS. The application server talks to a database over a private network. An email provider may be used for password-reset messages. The model is intentionally technology-neutral; confirm these assumptions against the real application before relying on the ratings.

## Deliverables
- `threat-model.md` — architecture, trust boundaries, STRIDE threats and mitigations.
- `risk-register.csv` — prioritized risks with likelihood, impact and treatment.
- `hardening-checklist.md` — prioritized implementation checklist.
- `threat-model.mmd` — editable Mermaid data-flow diagram.

## Risk scoring
Likelihood and impact are scored from 1 (low) to 5 (very high). Risk score = likelihood × impact:
- 1–4 Low
- 5–9 Medium
- 10–16 High
- 17–25 Critical

## Important
This is a threat-modeling deliverable, not proof that controls have been implemented or tested. Validate the architecture, owners, and controls with the application team. Do not place real credentials, personal data, production URLs, or secrets in this repository.
