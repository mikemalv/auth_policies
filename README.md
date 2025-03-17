# auth_policies
Find users with/without Authentication Policies

# Authentication Policy Management in Snowflake

This repository contains Snowflake stored procedures to help you manage and audit authentication policies across your Snowflake account.

## Overview

Authentication policies in Snowflake provide a way to configure and enforce authentication requirements for users, such as password complexity, MFA requirements, and session timeouts. These procedures help you identify which users have authentication policies assigned and which ones don't.

## Procedures

### 1. find_users_missing_authn_policies()

This procedure identifies all users in your Snowflake account who do not have an authentication policy assigned.

#### Usage

```sql
CALL find_users_missing_authn_policies();
```

#### Returns

A JSON object containing:
- `status`: "SUCCESS" if all users have policies, "WARNING" if some users are missing policies
- `message`: A summary of the findings
- `users`: An array of user objects with details about each user missing a policy

#### Example Output

```json
{
  "status": "WARNING",
  "message": "Found 5 users missing authentication policies",
  "users": [
    {
      "default_role": "ACCOUNTADMIN",
      "default_warehouse": "COMPUTE_WH",
      "disabled": false,
      "display_name": "Admin User",
      "email": "admin@example.com",
      "has_authentication_policy": false,
      "login_name": "ADMIN_USER",
      "name": "ADMIN_USER"
    },
    ...
  ]
}
```

### 2. find_users_with_authn_policies()

This procedure identifies all users in your Snowflake account who have an authentication policy assigned and shows which policy is applied to each user.

#### Usage

```sql
CALL find_users_with_authn_policies();
```

#### Returns

A JSON object containing:
- `status`: "SUCCESS" if users with policies are found, "WARNING" if no users have policies
- `message`: A summary of the findings
- `users`: An array of user objects with details about each user and their assigned policy

#### Example Output

```json
{
  "status": "SUCCESS",
  "message": "Found 10 users with authentication policies",
  "users": [
    {
      "name": "FINANCE_USER",
      "login_name": "FINANCE_USER",
      "display_name": "Finance Department User",
      "email": "finance@example.com",
      "default_role": "FINANCE_ROLE",
      "default_warehouse": "FINANCE_WH",
      "disabled": false,
      "has_authentication_policy": true,
      "authentication_policy_name": "FINANCE_AUTH_POLICY"
    },
    ...
  ]
}
```

## Implementation Details

Both procedures:
- Query the `INFORMATION_SCHEMA.POLICY_REFERENCES` view to check policy assignments
- Retrieve detailed user information from `SNOWFLAKE.ACCOUNT_USAGE.USERS`
- Return structured JSON output for easy integration with other tools or dashboards

## Use Cases

1. **Security Compliance Audits**: Quickly identify users who may not have proper authentication controls
2. **User Management**: Review authentication policy assignments across your organization
3. **Onboarding Verification**: Ensure new users are properly configured with authentication policies
4. **Policy Migration**: Help manage transitions between different authentication policies

## Requirements

- ACCOUNTADMIN or SECURITYADMIN role privileges to view policy references
- Access to ACCOUNT_USAGE schema

## Installation

Copy and execute the stored procedure definitions in your Snowflake account:

```sql
-- Create procedure to find users missing authentication policies
CREATE OR REPLACE PROCEDURE find_users_missing_authn_policies()
  RETURNS VARIANT NOT NULL
  LANGUAGE JAVASCRIPT
  EXECUTE AS CALLER
  AS     
  $$
  -- Procedure code here
  $$;

-- Create procedure to find users with authentication policies
CREATE OR REPLACE PROCEDURE find_users_with_authn_policies()
  RETURNS VARIANT NOT NULL
  LANGUAGE JAVASCRIPT
  EXECUTE AS CALLER
  AS     
  $$
  -- Procedure code here
  $$;
```

## Best Practices

- Run these procedures regularly as part of your security audit process
- Consider setting up automation to alert when users are found without proper authentication policies
- Document and standardize which authentication policies should be used for different user types in your organization
