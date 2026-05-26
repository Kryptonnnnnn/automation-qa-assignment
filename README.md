# Automation & QA Developer Assignment

## Overview
This repository contains my submission for the Automation & QA Developer Take-Home Assessment.

The assignment includes:
- Task 1: QA Testing Report
- Task 2: n8n API Integration Workflow
- Screenshots and documentation

---

# Task 1 — QA Testing

## Application Tested
TodoMVC React Application

## Testing Performed
- Add Todo
- Complete Todo
- Delete Todo
- Edit Todo
- Refresh persistence testing
- Empty input validation
- Long text handling
- Special character handling

## Bugs Identified
1. Todo data disappears after browser refresh
2. Long text affects UI readability

## Deliverables
- QA PDF Report
- Screenshots of testing

---

# Task 2 — n8n API Integration Workflow

## APIs Used
- CoinGecko API
- Discord Webhook API

## Workflow Logic
1. Schedule Trigger runs automatically
2. CoinGecko API fetches cryptocurrency data
3. JavaScript node filters and processes results
4. IF node checks threshold condition
5. Discord webhook sends crypto alert message

## Features Implemented
- Schedule Trigger
- HTTP Request nodes
- JavaScript transformation
- Conditional IF logic
- Discord notification
- Error handling using Continue On Fail

## Error Handling
The workflow uses:
- Continue On Fail option
- Conditional execution path
- Safe execution without silent crashes

---

# Screenshots
Included:
- Workflow canvas
- Successful execution output

---

# Tools & Technologies
- n8n
- CoinGecko API
- Discord Webhooks
- GitHub
- Manual QA Testing

---
