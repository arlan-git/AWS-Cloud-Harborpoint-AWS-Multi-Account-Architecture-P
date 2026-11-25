# Design Decisions — HarborPoint AWS Architecture

This document explains my choices in the proposed design. I am approaching the scenario as a student learning AWS cloud architecture, so I focus on the core concepts and why each choice is useful.

## 1. Separate accounts for environments

I chose separate AWS accounts for Development, Test, and Production. Each environment has its own account boundary, resources, and access controls. This helps reduce the chance that a mistake in a non-production environment affects Production.

**Trade-off:** More accounts improve isolation and policy targeting, but they add operational work.

## 2. Dedicated security and shared-services accounts

I placed Log Archive and Security Tooling in a Security OU. Audit records should be stored with tightly controlled access, and security monitoring should not depend only on application teams.

I added a Shared Services account under Infrastructure for capabilities such as networking and DNS that may be used by multiple workloads.

## 3. Central workforce access

I selected AWS IAM Identity Center as the proposed central access method. Employees can be organized into groups, and permission sets can be assigned to those groups for specific accounts.

Group-based access is easier to manage consistently than separately assigning permissions to every employee in every account.

## 4. Least-privilege access

Developers receive their main write access in Development and limited access in Test. QA engineers receive their main test permissions in Test. Security analysts receive audit-oriented access and read-only visibility where needed.

I would avoid standing developer or QA write access in Production. Production changes should follow an approved release process.

## 5. Read-only S3 policy

The example policy permits listing the `reports/` prefix and reading objects beneath that prefix in one named bucket. It intentionally does not include upload, delete, or bucket-administration actions.

This demonstrates how an IAM policy can limit both the actions and resources available to a principal.

## 6. CloudTrail SCP guardrail

I used an explicit deny for `cloudtrail:StopLogging` as an example of an organization-level guardrail. The key lesson is that an SCP does not grant permissions; it limits the maximum permissions available to member-account principals. An applicable explicit deny overrides an IAM Allow, including an AdministratorAccess allow.

The assignment's exact SCP JSON should be checked if it differs from the simplified example in the README.

## 7. Control Tower and Landing Zone Accelerator

I would evaluate Control Tower as a managed foundation for the multi-account landing zone. I would then evaluate Landing Zone Accelerator if HarborPoint needs more detailed, configuration-driven security, networking, logging, or compliance controls.

I have not deployed either product. The project explains where they could fit and why they might be useful.

## 8. Items to validate before a real deployment

- Confirm the exact SCP content, attachment locations, and any conditions or exceptions.
- Define custom permission-set policies from actual employee responsibilities.
- Confirm the S3 bucket name, data classification, and required access paths.
- Decide the required AWS Regions and network connectivity model.
- Test policies and account guardrails in a non-production environment.
- Have security, cloud-platform, and application owners review the design.
