# Unit Testing Proposal — speedydd-apiservices

## Table of Contents

1. [Introduction](#1-introduction)

   1.1 [Purpose of the Proposal](#11-purpose-of-the-proposal)

   1.2 [Scope of Unit Testing](#12-scope-of-unit-testing)

   1.3 [Target Audience](#13-target-audience)


2. [Current Backend Architecture Overview](#2-current-backend-architecture-overview)

   2.1 [Technology Stack (Node.js, Express, Jest)](#21-technology-stack-nodejs-express-jest)

   2.2 [Layered Architecture Overview](#22-layered-architecture-overview)

   - Controller Layer

   - Service Layer

   - Data Access Layer (Repositories / Models)

   2.3 [Existing Testing Status (if any)](#23-existing-testing-status-if-any)

3. [Objectives of Unit Testing](#3-objectives-of-unit-testing)

   3.1 Improve Code Quality and Reliability

   3.2 Early Bug Detection

   3.3 Enable Safe Refactoring

   3.4 Increase Development Velocity

   3.5 Support CI/CD Pipelines

4. [Unit Testing Strategy](#4-unit-testing-strategy)

   4.1 Definition of Unit Testing

   4.2 Testing Pyramid Overview

   4.3 Test Coverage Goals

   4.4 What Is In Scope

   4.5 What Is Out of Scope

5. [Testing Tools and Frameworks](#5-testing-tools-and-frameworks)

   5.1 Jest Overview

   5.2 Supertest (for Controller Tests)

   5.3 Mocking Libraries and Techniques

   - Jest Mocks

   - Manual Mocks

   5.4 Code Coverage Tools

6. [Controller Layer Unit Testing](#6-controller-layer-unit-testing)

   6.1 Responsibilities of the Controller Layer

   6.2 What to Test in Controllers

   - Request validation

   - HTTP status codes

   - Response structure

   - Error handling

   6.3 What Not to Test in Controllers

   6.4 Mocking Service Dependencies

   6.5 Sample Controller Test Structure

   6.6 Common Edge Cases

7. [Service Layer Unit Testing](#7-service-layer-unit-testing)

   7.1 Responsibilities of the Service Layer

   7.2 What to Test in Services

   - Business logic

   - Data transformation

   - Error scenarios

   7.3 What Not to Test in Services

   7.4 Mocking Repositories / External Services

   7.5 Sample Service Test Structure

   7.6 Handling Async Logic and Exceptions

8. [Mocking Strategy](#8-mocking-strategy)
   
   8.1 Mocking External APIs

   8.2 Mocking Database Calls

   8.3 Mocking Third-Party Libraries
   
   8.4 Avoiding Over-Mocking

9.  [Test Data Management](#9-test-data-management)

   9.1 Test Fixtures

   9.2 Factory Functions

   9.3 Reusable Mock Data

   9.4 Maintaining Readable Test Data

10. [Folder Structure and Naming Conventions](#10-folder-structure-and-naming-conventions)

   10.1 Recommended Test Folder Structure

   10.2 File Naming Standards

   10.3 Test Case Naming Guidelines

11. [Code Coverage and Quality Metrics](#11-code-coverage-and-quality-metrics)

   11.1 Coverage Thresholds

   11.2 Measuring Coverage with Jest

   11.3 Interpreting Coverage Reports

   11.4 Handling Low-Coverage Areas

12. [CI/CD Integration](#12-cicd-integration)

   12.1 Running Tests in CI

   12.2 Failing Builds on Test Failure

   12.3 Coverage Enforcement in CI

   12.4 Test Performance Considerations

13. [Best Practices and Guidelines](#13-best-practices-and-guidelines)

   13.1 Writing Maintainable Tests

   13.2 AAA Pattern (Arrange–Act–Assert)

   13.3 Testing Happy Paths vs Edge Cases

   13.4 Keeping Tests Fast and Isolated

14. [Risks and Mitigation](#14-risks-and-mitigation)

   14.1 Initial Development Time

   14.2 Flaky Tests

   14.3 Maintenance Overhead

   14.4 Mitigation Strategies

15. [Implementation Plan](#15-implementation-plan)

   15.1 Phase 1: Setup and Configuration

   15.2 Phase 2: Service Layer Tests

   15.3 Phase 3: Controller Layer Tests

   15.4 Phase 4: CI Integration

   15.5 Phase 5: Review and Optimization

16. [Estimated Effort and Timeline](#16-estimated-effort-and-timeline)

   16.1 Effort Estimation per Module

   16.2 Timeline Overview

   16.3 Resource Allocation

17. [Conclusion](#17-conclusion)

   17.1 Summary of Benefits

   17.2 Expected Outcomes

   17.3 Next Steps

18. [Appendix](#18-appendix)

   18.1 Sample Test Code

   18.2 Jest Configuration Example

   18.3 Useful References

---

## 1. Introduction

### 1.1 Purpose of the Proposal

This document proposes a consistent and scalable unit testing approach for `speedydd-apiservices`. The goal is to make changes safer and faster by:

- Standardizing how we write and organize tests.
- Defining what “unit testing” means in this codebase (and what it does not).
- Establishing coverage goals and quality gates that support CI/CD without slowing development.

### 1.2 Scope of Unit Testing

Unit tests in this proposal focus on verifying **behavior** of individual modules (functions/classes) in isolation:

- Deterministic tests: no network calls, no real database connections, no filesystem I/O.
- Dependencies are mocked at module boundaries (database models, external services, context/auth helpers).
- Tests live under `src/__tests__/` and mirror the `src/` structure.

### 1.3 Target Audience

- Backend engineers working on controllers/services/models
- Tech leads/reviewers who enforce testing standards
- DevOps/CI maintainers who run tests in pipelines
- QA/Support engineers who need confidence in changes

---

## 2. Current Backend Architecture Overview

### 2.1 Technology Stack (Node.js, Express, Jest)

The backend is built with:

- **Node.js + TypeScript**
- **Express** for HTTP routing/controllers
- **MongoDB/Mongoose** (models under `src/database/models`)
- **Jest + ts-jest** for unit tests (`npm test`)
- **Path aliases** (e.g. `@src/*`, `@utils/*`) via Jest `moduleNameMapper`

### 2.2 Layered Architecture Overview

Typical request flow:

`routes` → `controllers` → `services` → `data access (models/repositories)` → `external systems`

#### Controller Layer

- Maps HTTP requests to application operations
- Performs request validation and response formatting
- Translates domain/service errors to HTTP status codes

#### Service Layer

- Implements business rules and orchestration
- Calls repositories/models and external integrations via wrappers/adapters
- Owns most decision logic and edge-case handling

#### Data Access Layer (Repositories / Models)

- Encapsulates persistence logic (queries, updates, transactions)
- Ideally keeps business rules out of persistence code
- Should be mocked in unit tests; integration tests cover DB wiring

### 2.3 Existing Testing Status (if any)

Current unit tests are primarily concentrated in policy and permission logic:

- `src/__tests__/policies/*.test.ts` (e.g. access policy validation)
- `src/__tests__/entityPolicies/validateUserProfile.test.ts` (user profile validation helpers)
- `src/__tests__/policy.test.ts` as an index/entry point for policy-related tests

This is a solid foundation for critical auth logic. The proposal expands coverage to:

- Service-level business logic in `src/services/**`
- Controller behavior (request/response/error mapping) where it adds value

---

## 3. Objectives of Unit Testing

### 3.1 Improve Code Quality and Reliability

- Make intended behavior explicit and reviewable
- Reduce regressions by catching breaking changes early

### 3.2 Early Bug Detection

- Catch edge cases at development time rather than in staging/production
- Fail fast when refactors introduce subtle logic changes

### 3.3 Enable Safe Refactoring

- Encourage improving readability and architecture with confidence
- Reduce fear of touching “high-risk” modules by adding tests first

### 3.4 Increase Development Velocity

- Faster feedback loops locally (`npm test -- --watch`)
- Smaller PR review cycles when tests document behavior

### 3.5 Support CI/CD Pipelines

- Provide automated quality gates
- Prevent shipping obviously broken behavior

---

## 4. Unit Testing Strategy

### 4.1 Definition of Unit Testing

For `speedydd-apiservices`, a unit test should:

- Test one module’s behavior (function/class) at a time
- Mock all external dependencies: DB models, network clients, and request-scoped context
- Be deterministic and run quickly (milliseconds, not seconds)

### 4.2 Testing Pyramid Overview

- **Unit tests (most)**: fast, isolated, run on every PR
- **Integration tests (some)**: verify wiring with DB/services in controlled environments
- **End-to-end tests (few)**: validate critical user flows across the full stack

This proposal focuses on unit tests; integration/E2E are complementary but out of scope for this document.

### 4.3 Test Coverage Goals

Recommended phased goals (to avoid blocking the team immediately):

- **Phase 1 (baseline)**: generate coverage reports in CI (no enforcement)
- **Phase 2 (minimum gates)**: enforce a modest global threshold (e.g. 60–70%)
- **Phase 3 (target state)**: raise to 80%+ overall, higher for critical domains (auth/policies, billing)

Important: coverage is a signal, not the goal. Tests must assert meaningful behavior, not just execute lines.

### 4.4 What Is In Scope

- Pure utilities and helpers in `src/utils/**`
- Business logic in `src/services/**`
- Validation and mapping logic (DTOs/validators)
- Auth/policy rules (`src/libs/auth/**`)
- Controller tests that validate:
  - request validation
  - response shape/status
  - error mapping

### 4.5 What Is Out of Scope

- Real database access (no Mongo connections in unit tests)
- Real network calls (AWS/GCP/Microsoft/Onfido/Twilio/etc.)
- Performance/load testing
- End-to-end UI/user-flow testing

---

## 5. Testing Tools and Frameworks

### 5.1 Jest Overview

Jest is the primary unit testing framework:

- Run all tests: `npm test`
- Run a test file: `npm test -- src/__tests__/policies/hasAccessByPolicy.test.ts`
- Run by name: `npm test -- -t "should grant access"`
- Watch mode: `npm test -- --watch`
- Coverage: `npm test -- --coverage`

Jest is configured in `jest.config.js` (see Appendix for an example).

### 5.2 Supertest (for Controller Tests)

To unit-test controller + route behavior without a real server, **Supertest** is a common choice:

- Pros: realistic HTTP assertions (status/headers/body), minimal boilerplate
- Cons: requires Express app/router wiring; may resemble light integration tests

If/when controller tests are introduced, consider adding:

```bash
npm i -D supertest @types/supertest
```

### 5.3 Mocking Libraries and Techniques

#### Jest Mocks

Recommended patterns:

- `jest.mock("module")` for module-level isolation
- `jest.fn()` for lightweight function mocks
- `jest.spyOn(obj, "method")` when you need partial mocking
- `mockResolvedValue` / `mockRejectedValue` for async flows
- `jest.clearAllMocks()` in `beforeEach()` to avoid leaking state between tests

#### Manual Mocks

Use manual mocks when:

- A dependency is reused across many tests (e.g. context/auth helpers)
- You want consistent, readable default behaviors

Common patterns:

- `__mocks__/` directory for stable module mocks
- Shared factories/fixtures under `src/__tests__/fixtures` and `src/__tests__/factories`

### 5.4 Code Coverage Tools

Jest uses Istanbul for coverage reporting:

- Generate: `npm test -- --coverage`
- Output: `coverage/` (HTML report usually at `coverage/lcov-report/index.html`)

---

## 6. Controller Layer Unit Testing

### 6.1 Responsibilities of the Controller Layer

Controllers should be thin:

- Validate and normalize request inputs
- Call service layer operations
- Translate results to HTTP responses
- Translate errors to HTTP status codes (and stable error payloads)

### 6.2 What to Test in Controllers

- **Request validation**: required params/body/query, format checks, schema validation
- **HTTP status codes**: 200/201/204 for success, 400/401/403/404/409/422/500 as appropriate
- **Response structure**: stable JSON shape, required fields, paging metadata
- **Error handling**: correct mapping for domain errors and unexpected exceptions

### 6.3 What Not to Test in Controllers

- Deep business logic (belongs in services)
- Database query correctness (belongs in repository/model tests or integration tests)
- Third-party API behavior (mock the wrapper and test your mapping logic)

### 6.4 Mocking Service Dependencies

Recommended approach:

- Mock service modules called by the controller.
- Assert the controller passes the expected input to the service.
- Assert the controller maps the service result/error to the expected HTTP response.

If controllers are tightly coupled to imported services, module mocking works well:

- `jest.mock("@src/services/...")`

If the codebase uses dependency injection in some areas, prefer injecting mocks to reduce brittle tests.

### 6.5 Sample Controller Test Structure

See Appendix 18.1 for an example using an HTTP-style test (recommended when using Supertest).

### 6.6 Common Edge Cases

- Missing/invalid auth context
- Missing required fields (query/body/params)
- Invalid IDs (ObjectId format), invalid enums, invalid date ranges
- Service throws a known error type (map to 4xx)
- Service throws an unexpected error (map to 500 with safe message)

---

## 7. Service Layer Unit Testing

### 7.1 Responsibilities of the Service Layer

Services should own:

- Business rules and authorization decisions
- Coordination across repositories/models and external adapters
- Data transformation and validation beyond simple request shape validation

### 7.2 What to Test in Services

- **Business logic**: rules, decisions, branching behavior
- **Data transformation**: mapping between models, DTOs, view models
- **Error scenarios**: invalid states, missing data, permission denials

### 7.3 What Not to Test in Services

- Express request/response objects
- Mongoose internals (mock the model/repository boundary)
- Behavior of third-party SDKs (mock the adapter and test your usage contract)

### 7.4 Mocking Repositories / External Services

Prefer mocking at stable boundaries:

- **Database**: mock model methods (e.g. `findOne`, `updateOne`) or repository modules
- **External APIs**: wrap SDK calls in `src/utils/**` or `src/services/**/adapters` and mock the wrapper
- **Request context**: mock context helpers used by the service (e.g. current user/organisation helpers)

### 7.5 Sample Service Test Structure

Recommended skeleton:

- Arrange: build inputs and stub dependencies
- Act: call the service function
- Assert: verify result + verify dependency calls (only when meaningful)

See Appendix 18.1 for a service test example grounded in existing test patterns.

### 7.6 Handling Async Logic and Exceptions

Recommended patterns:

- Success: `mockResolvedValue(...)` + `await service()`
- Failure: `mockRejectedValue(new Error(...))` + `await expect(service()).rejects.toThrow(...)`
- Time-based logic: `jest.useFakeTimers()` and explicit timer advancement (avoid real timeouts)

---

## 8. Mocking Strategy

### 8.1 Mocking External APIs

- Create a thin wrapper module around each external SDK (S3, Google, Microsoft, Twilio, etc.)
- Mock the wrapper in unit tests, not the SDK internals
- Test your request/response mapping and error translation

### 8.2 Mocking Database Calls

- Do not connect to MongoDB in unit tests.
- Mock at the model/repository layer:
  - `jest.mock("@src/database/models/...")`
  - or `jest.spyOn(Model, "findOne").mockResolvedValue(...)`

### 8.3 Mocking Third-Party Libraries

- Prefer not to mock stable utility libraries (e.g. `lodash`) unless necessary
- If mocking is required, mock only the surface you depend on and keep assertions behavior-focused

### 8.4 Avoiding Over-Mocking

Over-mocking can make tests brittle and meaningless. Guardrails:

- If you mock most of the module under test, you are not testing behavior.
- Mock dependencies, not the logic you own.
- If a test needs too many mocks, consider splitting the module or using a higher-level integration test.

---

## 9. Test Data Management

### 9.1 Test Fixtures

Use fixtures for stable, reusable data:

- Small JSON payloads (requests/responses)
- Example documents (sanitized) representing common DB entities

### 9.2 Factory Functions

Factories reduce boilerplate and clarify intent:

- `createUser({ role: UserRoles.ADMIN })`
- `createOrganisation({ ...overrides })`

### 9.3 Reusable Mock Data

Proposed conventions:

- `src/__tests__/fixtures/**` for static objects
- `src/__tests__/factories/**` for builders/factories
- `src/__tests__/testUtils/**` for shared helpers (mock context, fake IDs, etc.)

### 9.4 Maintaining Readable Test Data

- Prefer explicit field overrides over huge objects
- Use named constants for “important” values (IDs, policy names)
- Keep one test focused on one behavior; avoid overloading a test with unrelated data

---

## 10. Folder Structure and Naming Conventions

### 10.1 Recommended Test Folder Structure

Mirror the `src/` structure to keep tests discoverable:

- `src/__tests__/controllers/**`
- `src/__tests__/services/**`
- `src/__tests__/utils/**`
- `src/__tests__/libs/**`

Existing suites such as `src/__tests__/policies/**` can remain, as they already group a cohesive domain.

### 10.2 File Naming Standards

- Unit tests: `*.test.ts` (or `*.spec.ts`)
- Optional future distinction (if added later):
  - Integration tests: `*.int.test.ts`
  - E2E tests: `*.e2e.test.ts`

### 10.3 Test Case Naming Guidelines

- Use descriptive names: `should <expected outcome> when <condition>`
- Keep `describe()` blocks meaningful:
  - `describe("when ...")`
  - `describe("error cases")`
  - `describe("edge cases")`

---

## 11. Code Coverage and Quality Metrics

### 11.1 Coverage Thresholds

Recommended approach:

- Start with reporting only (baseline)
- Add thresholds once the suite is stable
- Apply higher thresholds to critical domains (auth/policies, billing, permissions)

Example thresholds (proposal, not mandatory on day one):

- Global: 80% lines/statements, 70% branches
- Critical packages: 90% lines/statements, 80% branches

### 11.2 Measuring Coverage with Jest

```bash
npm test -- --coverage
```

### 11.3 Interpreting Coverage Reports

- **Statements/Lines**: general execution signal
- **Branches**: ensures if/else paths are tested (often most valuable)
- **Functions**: indicates callable behavior coverage

Use coverage reports to identify risk areas, but validate with real assertions.

### 11.4 Handling Low-Coverage Areas

- Prioritize by risk and change frequency (auth, billing, permissions first)
- Use “boy scout rule”: add/extend tests when touching a module
- Avoid chasing 100%: focus on meaningful behaviors and edge cases

---

## 12. CI/CD Integration

### 12.1 Running Tests in CI

Typical CI steps:

```bash
npm ci
npm test -- --ci
```

If you want coverage artifacts:

```bash
npm test -- --ci --coverage
```

### 12.2 Failing Builds on Test Failure

Jest exits with a non-zero code when tests fail, which should fail CI by default.

### 12.3 Coverage Enforcement in CI

Once ready, enforce coverage via Jest config (`coverageThreshold`) or CI checks that parse the coverage summary.

### 12.4 Test Performance Considerations

- Keep unit tests fast and isolated (avoid heavy setup)
- Prefer targeted tests per PR when the suite grows
- Use `--runInBand` only if CI has limited resources (slower but stable)

---

## 13. Best Practices and Guidelines

### 13.1 Writing Maintainable Tests

- Test behavior, not implementation details
- Keep tests independent and deterministic
- Prefer small, focused tests over “mega tests”

### 13.2 AAA Pattern (Arrange–Act–Assert)

- **Arrange**: setup inputs + mocks
- **Act**: call the unit under test
- **Assert**: verify outputs and important side effects

### 13.3 Testing Happy Paths vs Edge Cases

- Cover the happy path first (primary behavior)
- Add edge cases (null/empty, invalid inputs, boundary conditions)
- Add error cases (expected errors and unexpected exceptions)

### 13.4 Keeping Tests Fast and Isolated

- No real DB/network
- Minimize global shared state
- Reset mocks between tests (`jest.clearAllMocks()`)

---

## 14. Risks and Mitigation

### 14.1 Initial Development Time

Risk: writing tests adds up-front cost.

Mitigation:

- Start with critical modules and high-change areas
- Add tests incrementally alongside feature work

### 14.2 Flaky Tests

Risk: nondeterministic tests waste time and reduce trust.

Mitigation:

- Avoid real timers and external I/O
- Use fake timers for time-based logic
- Keep mocks explicit and reset between tests

### 14.3 Maintenance Overhead

Risk: tests become a burden if coupled to implementation details.

Mitigation:

- Test externally observable behavior and contracts
- Prefer stable boundaries (service contracts, adapters)
- Refactor tests as part of refactoring code

### 14.4 Mitigation Strategies

- Establish shared templates and test utilities
- Require tests for bug fixes and risky changes
- Add lightweight review checklist: “Does the test prove behavior?”

---

## 15. Implementation Plan

### 15.1 Phase 1: Setup and Configuration

- Confirm Jest + ts-jest config is stable
- Define conventions (folders, naming, test utilities)
- Establish baseline coverage reporting in CI (no enforcement yet)

### 15.2 Phase 2: Service Layer Tests

- Prioritize critical service domains first (auth, permissions, billing, user/org)
- Add tests for pure helpers and business rules
- Introduce factories/fixtures to keep tests readable

### 15.3 Phase 3: Controller Layer Tests

- Add controller tests for high-traffic or high-risk endpoints
- Consider Supertest for request/response behavior verification
- Keep controller tests thin and focused on HTTP mapping

### 15.4 Phase 4: CI Integration

- Run `npm test -- --ci` on PRs
- Add coverage artifacts and (later) coverage thresholds
- Fail build on regressions

### 15.5 Phase 5: Review and Optimization

- Reduce duplication via shared factories/helpers
- Remove brittle tests and replace with behavior-based assertions
- Improve runtime (parallelization, pruning redundant tests)

---

## 16. Estimated Effort and Timeline

### 16.1 Effort Estimation per Module

Rough guidance (depends heavily on complexity and current testability):

- Small utility module: 0.5–1 hour
- Medium service with several branches: 2–6 hours
- Complex service integrating multiple dependencies: 1–2 days
- Controller suite for a module: 0.5–2 days (depending on endpoints and setup)

### 16.2 Timeline Overview

Suggested timeline:

- Week 1: Phase 1 + start Phase 2 (critical services)
- Week 2–3: Expand Phase 2 across top modules
- Week 3–4: Start Phase 3 (controllers) + Phase 4 (CI gates)

### 16.3 Resource Allocation

- 1–2 backend engineers to drive initial adoption and create templates
- Code review support from tech lead
- CI support from DevOps as needed

---

## 17. Conclusion

### 17.1 Summary of Benefits

- Fewer regressions and safer deployments
- Faster feedback loops during development
- Easier refactoring and onboarding (tests explain behavior)

### 17.2 Expected Outcomes

- Broader unit test coverage beyond policies
- Stable testing conventions and shared patterns
- CI/CD quality gates aligned with team velocity

### 17.3 Next Steps

- Agree on scope and thresholds for Phase 1–2
- Identify 3–5 critical service modules to test first
- Establish shared factories/test utilities as early groundwork

---

## 18. Appendix

### 18.1 Sample Test Code

#### Example A: Pure policy function unit test (existing pattern)

```ts
import FEATURE_AUTH_POLICIES from "@src/libs/auth/authorizePolicy";
import { hasAccessByPolicy } from "@src/libs/auth/policy";

describe("hasAccessByPolicy", () => {
  it("should grant access when user has the exact policy ID", () => {
    expect(
      hasAccessByPolicy(FEATURE_AUTH_POLICIES.COMPANY.DETAIL, [
        FEATURE_AUTH_POLICIES.COMPANY.DETAIL
      ])
    ).toBe(true);
  });
});
```

#### Example B: Service/helper test with module mocks (existing pattern)

```ts
import { BadRequestError } from "@src/utils/httpErrors";
import validateUserProfile from "@src/services/user/helpers/validateUserProfile";

jest.mock("@src/libs/auth/authorizePolicy", () => ({
  __esModule: true,
  default: { COMPANY: { ALL: "company.all" } },
  POLICY_SEPARATOR: "::",
  AccessLevel: { READ: "read", WRITE: "write" },
  FLATTEN_POLICIES: ["company.all"]
}));

describe("validateUserProfile", () => {
  it("should throw for invalid permission format", () => {
    expect(() =>
      validateUserProfile({
        organisation: "fake",
        role: "user",
        pageAccess: ["invalid-format"],
        entityPolicies: { companies: [], persons: [] }
      } as any)
    ).toThrow(BadRequestError);
  });
});
```

#### Example C: Controller test (proposal) using Supertest

```ts
// This example assumes you expose an Express app/router for testing.
// If not available yet, consider adding a small test-only app builder.
import request from "supertest";
import app from "@src/server"; // or a dedicated app factory

describe("GET /health", () => {
  it("should return 200 with expected payload", async () => {
    const res = await request(app).get("/health").expect(200);
    expect(res.body).toEqual({ status: "ok" });
  });
});
```

### 18.2 Jest Configuration Example

Current Jest config lives in `jest.config.js`. Typical settings:

```js
module.exports = {
  preset: "ts-jest",
  testEnvironment: "node",
  moduleNameMapper: {
    "^@src/(.*)$": "<rootDir>/src/$1",
    "^@customTypes/(.*)$": "<rootDir>/src/types/$1",
    "^@utils/(.*)$": "<rootDir>/src/utils/$1",
    "^@middleware/(.*)$": "<rootDir>/src/middleware/$1",
    "^@constant/(.*)$": "<rootDir>/src/constant/$1"
  },
  testMatch: ["**/__tests__/**/*.ts?(x)", "**/?(*.)+(spec|test).ts?(x)"],
  moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"]
};
```

Optional future enhancements (as the suite grows):

- `clearMocks: true` to reduce boilerplate
- `setupFilesAfterEnv` for shared test setup (e.g. custom matchers)
- `coverageThreshold` once the baseline is established

### 18.3 Useful References

- Jest: https://jestjs.io/docs/getting-started
- ts-jest: https://kulshekhar.github.io/ts-jest/
- Supertest: https://github.com/ladjs/supertest
- JavaScript testing best practices: https://github.com/goldbergyoni/javascript-testing-best-practices
