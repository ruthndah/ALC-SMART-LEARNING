# BPM Tools — Architecture & Implementation Plan

## Product direction
Biodiesel Plant Management Tools is a multi-plant operational support application. It starts as a personal workspace and is designed to support team access later.

## Initial scope
- Plant Existing and Plant Baru as separate plant contexts.
- Shift Logbook: shift, timestamp, process area, category, operational notes, handover.
- Abnormality Register: area/equipment, severity, description, investigation, owner, status.
- Action Tracker: action, PIC, due date, priority, source, status.
- Process Notes: technical notes and lessons learned with reference documents.
- People & Team: planned module for manpower, competency matrix, and coaching.

## Proposed production architecture
- Frontend: static web application (Cloudflare Pages).
- API: Cloudflare Workers; validate inputs and enforce authorization server-side.
- Database: Cloudflare D1 (SQLite) as the operational system of record.
- File storage: Cloudflare R2 for approved documents and attachments, if required.
- Source control: GitHub.

## Data model proposal
- plants(id, code, name, is_active)
- areas(id, plant_id, code, name, process_group, is_active)
- users(id, display_name, email, role, is_active)
- user_plant_access(user_id, plant_id, access_role)
- shifts(id, plant_id, shift_code, start_at, end_at)
- shift_logs(id, plant_id, area_id, shift_id, logged_by, occurred_at, category, details, handover, created_at)
- abnormalities(id, plant_id, area_id, equipment_tag, reported_by, occurred_at, severity, description, investigation, owner_id, status, closed_at)
- actions(id, plant_id, source_type, source_id, title, owner_id, due_at, priority, status, completed_at)
- process_notes(id, plant_id, area_id, title, content, reference, author_id, created_at, updated_at)
- audit_events(id, plant_id, actor_id, entity_type, entity_id, action, created_at)

All operational records should carry plant_id. Use stable IDs, timestamps, and audit events for important changes. Do not rely on browser localStorage for shared or official operational records.

## Security and reliability principles
- Authentication and role-based authorization before enabling team access.
- Enforce plant access in every API request, not only in the UI.
- Validate and normalize inputs server-side.
- Keep secrets out of frontend code and GitHub.
- Back up D1 and define retention/export procedures.
- Do not store passwords, sensitive personnel evaluations, or confidential plant documents in the prototype.
- Treat the prototype as a personal draft tool only; do not use it as a replacement for DCS, interlocks, approved SOPs, permits, or official shift/production records.

## Delivery phases
1. Prototype UX and local testing (current branch).
2. D1 schema, migrations, and seed records for plant/area masters.
3. Workers API and frontend integration.
4. Authentication, roles, and plant-level access controls.
5. Operational modules: logs, abnormalities, action tracker.
6. Production/process metrics, chemical/material tracking, maintenance.
7. People/competency, reporting, analytics, audit and governance.

## Current prototype limitation
The current index.html stores demonstration records in the browser's localStorage, separately per selected plant. This is not shared across devices or users, is not backed up as an operational database, and is not suitable for official records.
