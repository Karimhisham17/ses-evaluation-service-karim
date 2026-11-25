# ses-evaluation-service-karim

Personal proof-of-work repository of **Karim Hisham Ebrahim Abdelbaset Mohamed (249581)** for the Group 26 coursework
*Supplier Directory & Evaluation System* (Cloud Computing and Cloud Security, 26CSCN29H).

The complete, runnable, integrated system (all three services, shared login, deployment to Floci, tests,
report) is in the team repository: https://github.com/Zeyad99Ahmed/supplier-evaluation-system (branch `karim`). This repository contains only the
component I own, so it is not meant to run on its own.

## Owned component: Evaluation Service (+ database setup and external API lead)

- F1 Rate a delivered order once, weighted score, score pushed to Supplier - POST /api/evaluations (Staff)
- F2 Supplier performance report - GET /api/performance/suppliers/:id
- F3 Compare 2-5 suppliers - GET /api/performance/compare
- F4 Rating history with filters - GET /api/evaluations
- F5 Edit / delete own rating within 7 days - PUT / DELETE /api/evaluations/:id
- F6 Exchange rates and conversion with the ExchangeRate API - GET /api/rates/:currency, /api/rates/convert, POST /internal/convert

## How it works with the other services

- Evaluation -> Order: is the order DELIVERED and raised by this user?
- Evaluation -> Supplier: new average (may set UNDER_REVIEW)
- Order and Supplier -> Evaluation: conversion of order and contract values to EGP

## Security controls I implemented

- Only Staff can rate; only the author can edit/delete (others 404)
- Scores validated 1-5 in the API and by a database CHECK; UNIQUE(order_id)
- API key only in Secrets Manager / git-ignored env file, never logged
- Provider response validated, 5 s timeout, cache and stale fallback
- Per-service database roles and least-privilege grants (database/02_roles_grants.sql)

## AWS services used by my component (deployed on Floci)

- AWS Lambda ses-evaluation-service
- Amazon API Gateway ses-evaluation-service-api
- AWS IAM role ses-evaluation-service-role
- AWS Secrets Manager ses/evaluation-service (ExchangeRate API key)
- Amazon CloudWatch Logs + ExchangeApiFailures metric

No credentials are stored in this repository: secrets come from AWS Secrets Manager on Floci and from a
git-ignored `.env.local` file during local development.
