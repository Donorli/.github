# Ways of Working

1. [Orientation](#orientation)
2. [Local Development Setup](#local-development-setup)
3. [Git & Branching Strategy](#git--branching-strategy)
4. [Linear Workflow](#linear-workflow)
5. [Development Workflow](#development-workflow)
6. [Testing Standards](#testing-standards)
7. [Database Migration Workflow](#database-migration-workflow)
8. [Code Review](#code-review)
9. [Environments & Deployment](#environments--deployment)
10. [Source Versioning](#source-versioning)
11. [Dependency Management](#dependency-management)

## Orientation
#### App map
#### active vs. frozen apps
#### Naming Conventions

## Local Development Setup
#### Prerequisites
#### environment variables
#### Stripe CLI. 

## Git & Branching Strategy
#### Branch types
#### naming convention (includes Linear issue ID)
#### Commits
###### Message Format (Linear-prefixed, conventional commits)
###### Commit Hygiene

## Linear Workflow
#### Ticket Format and Template
#### Issue Lifecycle
#### GitHub Integration (auto-status updates on branch creation, PR open, and merge)
#### Ticket Hygiene Rules. 

## Development Workflow
#### Test-driven development
Test driven development is the primary approach (Red / Green / Refactor cycle)
#### Commit Sequence Discipline
#### Coding Standards

## Testing Standards
TDD philosophy (tests are the specification), what to test at each layer (API routes, database / RLS, Stripe webhooks, React components, utilities, AI), running tests, coverage expectations, test data and Supabase. 

## Database Migration Workflow
Append-only migration rules, RLS policy requirements, staging-first mandate, rollback documentation. 

## Code Review
#### PR Checklist
#### Approval Thresholds
* None for staging
* Two (2) approvals for main / production)
* Same-business-day review SLA
* Reviewer checklist (TDD discipline is item 1). 

## Environments & Deployment
#### Environment map
(local → staging → production)
#### Deployment Rules
#### Feature flag approach
#### Smoke test requirements

## Source Versioning
#### Calendar versioning
(CalVer YYYY.MM.DD) per component
#### GitHub 
Release process, when to cut a release, rollback reference via Vercel.

## Dependency Management
#### Legacy peer-deps policy for admin app
#### Security Patch Response Windows
* Critical: 48 h
* High: 1 week
* Medium + Low: next sprint


