# Codebase overview

Last verified: {{DATE}}. Read this before touching code you have not mapped yet.
Keep it under ~150 lines; details belong in the area files.

## Purpose and users
{{APP_PURPOSE_DETAIL}}
<!-- what the app does, who uses it, the main jobs they come to do -->

## Layout
{{LAYOUT}}
<!-- top-level folders, one line each: path → what lives there -->

## Entry points and data flow
{{DATA_FLOW}}
<!-- where execution starts (server, CLI, pages, jobs) and how a typical request
     or action travels through the code, e.g. route → handler → service → model -->

## Areas
| Area | Paths | Map |
|---|---|---|
{{AREA_TABLE}}
<!-- every area; Map = docs/codebase/<area>.md, or "not mapped yet" -->

## Critical flows and no-go zones
{{CRITICAL_FLOWS}}
<!-- what must never break (and how it is tested); folders not to touch, and why -->

## External systems
{{EXTERNAL_SYSTEMS}}
<!-- databases, queues, third-party APIs, auth providers; where they are configured -->

## Direction and constraints
{{DIRECTION}}
<!-- legacy vs current, planned changes, performance/hosting/compliance limits -->
