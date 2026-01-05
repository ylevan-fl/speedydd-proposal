# Proposal: Frontend Unit & Component Testing for SpeedyDD Frontend

## Table of Contents

1. [Introduction](#1-introduction)

   1.1 [Purpose of the Proposal](#11-purpose-of-the-proposal)

   1.2 [Scope of Frontend Testing](#12-scope-of-frontend-testing)

   1.3 [Target Audience](#13-target-audience)

2. [Frontend Architecture Overview](#2-frontend-architecture-overview)

   2.1 [Technology Stack](#21-technology-stack)

   2.2 [Application Architecture Overview](#22-application-architecture-overview)

   2.3 [Current Testing Status](#23-current-testing-status)

3. [Objectives of Frontend Testing](#3-objectives-of-frontend-testing)

   3.1 Improve UI Stability and Reliability

   3.2 Early Detection of UI and Logic Bugs

   3.3 Protect Complex Business Flows

   3.4 Support Safe Refactoring

   3.5 Increase Confidence in Releases

4. [Frontend Testing Strategy](#4-frontend-testing-strategy)

   4.1 [Definition of Unit and Component Testing](#41-definition-of-unit-and-component-testing)

   4.2 [Frontend Testing Pyramid](#42-frontend-testing-pyramid)

   4.3 [Test Coverage Goals](#43-test-coverage-goals)

   4.4 [In-Scope Testing Areas](#44-in-scope-testing-areas)

   4.5 [Out-of-Scope Testing Areas](#45-out-of-scope-testing-areas)

5. [Testing Tools and Frameworks](#5-testing-tools-and-frameworks)

   5.1 [Jest](#51-jest)

   5.2 [React Testing Library](#52-react-testing-library)

   5.3 [Cypress (E2E Context)](#53-cypress-e2e-context)

   5.4 [Mocking and Test Utilities](#54-mocking-and-test-utilities)

6. [Component Testing Strategy](#6-component-testing-strategy)

   6.1 [Responsibilities of UI Components](#61-responsibilities-of-ui-components)

   6.2 [What Should Be Tested](#62-what-should-be-tested)

   6.3 [What Should Not Be Tested](#63-what-should-not-be-tested)

   6.4 [Testing Reusable Components (`src/components/`)](#64-testing-reusable-components-srccomponents)

   6.5 [Testing Feature Components (`src/blocks/`)](#65-testing-feature-components-srcblocks)

   6.6 [Common Edge Cases](#66-common-edge-cases)

7. [Form Testing Strategy](#7-form-testing-strategy)

   7.1 [React Hook Form Testing Scope](#71-react-hook-form-testing-scope)

   7.2 [Zod Validation Testing](#72-zod-validation-testing)

   7.3 [Form Submission and Error States](#73-form-submission-and-error-states)

   7.4 [Testing Complex Forms (`src/forms/`)](#74-testing-complex-forms-srcforms)

8. [Hooks Testing Strategy](#8-hooks-testing-strategy)

   8.1 [Custom Hook Responsibilities](#81-custom-hook-responsibilities)

   8.2 [What Should Be Tested in Hooks](#82-what-should-be-tested-in-hooks)

   8.3 [Testing Hooks with React Testing Library](#83-testing-hooks-with-react-testing-library)

   8.4 [Mocking Dependencies in Hooks](#84-mocking-dependencies-in-hooks)

9. [Service and API Layer Testing](#9-service-and-api-layer-testing)

   9.1 [Role of `src/services/`](#91-role-of-srcservices)

   9.2 [What Should Be Tested](#92-what-should-be-tested)

   9.3 [Mocking API Calls](#93-mocking-api-calls)

   9.4 [Integration with React Query](#94-integration-with-react-query)

10. [State Management Testing](#10-state-management-testing)

   10.1 [React Query Testing Scope](#101-react-query-testing-scope)

   10.2 [Zustand Store Testing](#102-zustand-store-testing)

   10.3 [Context Provider Testing](#103-context-provider-testing)

   10.4 [State Isolation and Test Stability](#104-state-isolation-and-test-stability)

11. [Routing and Middleware Testing](#11-routing-and-middleware-testing)

   11.1 [App Router Testing Considerations](#111-app-router-testing-considerations)

   11.2 [Protected Routes Testing](#112-protected-routes-testing)

   11.3 [Middleware Logic Testing (`src/middleware.ts`)](#113-middleware-logic-testing-srcmiddlewarets)

   11.4 [Redirect and Access Control Scenarios](#114-redirect-and-access-control-scenarios)

12. [Mocking Strategy](#12-mocking-strategy)

   12.1 [Mocking API Services](#121-mocking-api-services)

   12.2 [Mocking Browser APIs](#122-mocking-browser-apis)

   12.3 [Mocking Third-Party Libraries](#123-mocking-third-party-libraries)

   12.4 [Avoiding Over-Mocking](#124-avoiding-over-mocking)

13. [Test Data Management](#13-test-data-management)

   13.1 [Test Fixtures](#131-test-fixtures)

   13.2 [Factory Functions](#132-factory-functions)

   13.3 [Reusable Mock Data](#133-reusable-mock-data)

   13.4 [Maintainable Test Data Design](#134-maintainable-test-data-design)

14. [Folder Structure and Naming Conventions](#14-folder-structure-and-naming-conventions)

   14.1 [Recommended Test Folder Structure](#141-recommended-test-folder-structure)

   14.2 [File Naming Standards](#142-file-naming-standards)

   14.3 [Test Case Naming Guidelines](#143-test-case-naming-guidelines)

15. [Code Coverage and Quality Metrics](#15-code-coverage-and-quality-metrics)

   15.1 [Coverage Targets](#151-coverage-targets)

   15.2 [Measuring Coverage with Jest](#152-measuring-coverage-with-jest)

   15.3 [Interpreting Coverage Reports](#153-interpreting-coverage-reports)

   15.4 [Handling Low Coverage Areas](#154-handling-low-coverage-areas)

16. [CI/CD Integration](#16-cicd-integration)

   16.1 [Running Frontend Tests in CI](#161-running-frontend-tests-in-ci)

   16.2 [Build Fail Conditions](#162-build-fail-conditions)

   16.3 [Coverage Enforcement](#163-coverage-enforcement)

   16.4 [Test Performance Considerations](#164-test-performance-considerations)

17. [Best Practices and Guidelines](#17-best-practices-and-guidelines)

   17.1 [Writing Maintainable Frontend Tests](#171-writing-maintainable-frontend-tests)

   17.2 [Arrange–Act–Assert Pattern](#172-arrangeactassert-pattern)

   17.3 [Happy Path vs Edge Case Testing](#173-happy-path-vs-edge-case-testing)

   17.4 [Keeping Tests Fast and Deterministic](#174-keeping-tests-fast-and-deterministic)

18. [Risks and Mitigation](#18-risks-and-mitigation)

   18.1 [Large Codebase Complexity](#181-large-codebase-complexity)

   18.2 [Flaky UI Tests](#182-flaky-ui-tests)

   18.3 [Test Maintenance Cost](#183-test-maintenance-cost)

   18.4 [Mitigation Strategies](#184-mitigation-strategies)

19. [Implementation Plan](#19-implementation-plan)

   19.1 [Phase 1: Test Environment Setup](#191-phase-1-test-environment-setup)

   19.2 [Phase 2: Core Component and Hook Tests](#192-phase-2-core-component-and-hook-tests)

   19.3 [Phase 3: Form and Service Tests](#193-phase-3-form-and-service-tests)

   19.4 [Phase 4: Middleware and Routing Tests](#194-phase-4-middleware-and-routing-tests)

   19.5 [Phase 5: CI Integration and Review](#195-phase-5-ci-integration-and-review)

20. [Estimated Effort and Timeline](#20-estimated-effort-and-timeline)

   20.1 [Effort Estimation by Feature Area](#201-effort-estimation-by-feature-area)

   20.2 [Timeline Overview](#202-timeline-overview)

   20.3 [Resource Allocation](#203-resource-allocation)

21. [Conclusion](#21-conclusion)

   21.1 [Summary of Benefits](#211-summary-of-benefits)

   21.2 [Expected Outcomes](#212-expected-outcomes)

   21.3 [Next Steps](#213-next-steps)

22. [Appendix](#22-appendix)

   22.1 [Sample Component Test](#221-sample-component-test)

   22.2 [Sample Hook Test](#222-sample-hook-test)

   22.3 [Jest and RTL Configuration Examples](#223-jest-and-rtl-configuration-examples)

---

## 1. Introduction

### 1.1 Purpose of the Proposal

This document proposes a consistent, scalable approach to frontend unit and component testing for the SpeedyDD frontend application (Next.js App Router). The primary goals are:

- Standardize how tests are written, organized, and reviewed.
- Improve confidence when shipping changes across a large UI codebase.
- Reduce regressions in critical UI flows (auth, onboarding, documents, reports, mobile).
- Enable safer refactoring (especially for shared components, hooks, and services).

### 1.2 Scope of Frontend Testing

This proposal focuses on:

- Unit tests for pure logic: `src/utils/`, `src/libs/`, `src/constants/`, and selected `src/services/` helpers.
- Component tests for React UI: shared `src/components/` and feature-level `src/blocks/`.
- Hook tests for `src/hooks/` (including hooks built on TanStack Query and Zustand).
- Form tests for `src/forms/` using React Hook Form and Zod.
- Middleware tests for `src/middleware.ts`.

This proposal does not replace E2E testing; it complements it by providing fast, deterministic tests closer to the code.

### 1.3 Target Audience

- Frontend engineers who implement features in the frontend project
- Tech leads and reviewers who enforce testing standards
- QA and DevOps who rely on automated signals in CI
- Engineers contributing to shared packages (services, libs, middleware)

---

## 2. Frontend Architecture Overview

### 2.1 Technology Stack

The current frontend stack (as implemented in the frontend project) includes:

- Next.js 13.5.x (App Router)
- React 18.2.x
- TypeScript 5.6.x (strict mode enabled)
- Tailwind CSS
- TanStack Query (React Query) for data fetching and caching
- Zustand for local/global client state
- React Hook Form + Zod for forms and validation
- Axios for HTTP requests (`src/services/baseRequest.ts`)
- Cypress 12.x for E2E testing (typically under `cypress/`)
- Jest 29.x for unit/component testing (`jest.config.js`)

### 2.2 Application Architecture Overview

The `src/` directory is organized by responsibility:

- `src/app/`: Next.js App Router routes (including `/lobby`, `/mobile`, `/trial`, and public routes)
- `src/components/`: reusable UI components
- `src/blocks/`: feature blocks (composed views with business behavior)
- `src/forms/`: form implementations and related components
- `src/hooks/`: custom hooks (including data-fetching hooks)
- `src/services/`: API layer and request clients
- `src/context/` and `src/providers/`: app-level providers (React Context, Query client providers, etc.)
- `src/libs/`: cross-cutting helpers (auth, middleware pipeline, shared logic)
- `src/middleware.ts`: Next.js middleware pipeline entry

From a testing perspective, the main boundaries are:

- UI (components/blocks/app routes) should be tested with React Testing Library.
- Business logic (hooks/services/libs/utils) should be unit-tested with minimal DOM rendering.
- Requests should be mocked at the HTTP-client boundary (Axios or service module boundary).

### 2.3 Current Testing Status

As of today:

- Jest is configured in `jest.config.js` and runnable via `npm test` from the frontend project root.
- There is an initial middleware unit test at `src/__tests__/middleware.test.ts`.
- Cypress exists for E2E testing, but there is no systematic unit/component coverage across `src/components/`, `src/blocks/`, `src/forms/`, or `src/hooks/`.

This proposal defines how to expand from the current baseline to broad coverage in a controlled, maintainable way.

---

## 3. Objectives of Frontend Testing

### 3.1 Improve UI Stability and Reliability

- Reduce UI regressions by catching common rendering and interaction bugs early.
- Ensure core screens remain functional across refactors.

### 3.2 Early Detection of UI and Logic Bugs

- Catch issues in hooks, forms, and services before integration into larger flows.
- Detect incorrect assumptions around state, permissions, and access control.

### 3.3 Protect Complex Business Flows

Prioritize tests around:

- Authentication and authorization behavior
- Onboarding and profile selection flows
- Document viewing, signing, and uploading flows
- Permissions and conditional navigation (including mobile routes)

### 3.4 Support Safe Refactoring

- Enable refactoring shared components and hooks with confidence.
- Make architecture changes safer (e.g., moving logic between blocks/components/services).

### 3.5 Increase Confidence in Releases

- Provide quick feedback in CI for PRs.
- Reduce reliance on manual regression testing for common flows.

---

## 4. Frontend Testing Strategy

### 4.1 Definition of Unit and Component Testing

- Unit tests validate a small unit of logic in isolation (function, helper, store action, middleware handler).
- Component tests validate behavior of React components: rendering, user interactions, conditional UI, and integration with internal helpers (mocking external APIs).

Principles:

- Tests should be deterministic: no real network calls, no reliance on real time, no hidden global state.
- Mock at module boundaries (services, browser APIs, third-party SDKs), not at every internal function.
- Prefer behavioral assertions (what the user sees/does) over implementation assertions.

### 4.2 Frontend Testing Pyramid

Recommended distribution:

- Many unit tests for `src/utils/`, `src/libs/`, `src/services/` request helpers, and store logic.
- A smaller number of component tests for shared components and critical blocks.
- E2E tests (Cypress) remain limited to end-to-end user flows and smoke regression, not deep edge cases.

### 4.3 Test Coverage Goals

Because the codebase is large, coverage targets should be phased:

- Short term: establish a baseline and prevent regression by enforcing coverage for new/modified code paths.
- Mid term: expand coverage on high-risk modules (auth/middleware/services/hooks/forms).
- Long term: gradually increase overall coverage while keeping tests fast and stable.

Coverage enforcement details are described in section 15.

### 4.4 In-Scope Testing Areas

- Shared components in `src/components/` (inputs, tables, navigation, modals)
- Feature blocks in `src/blocks/` where UI behavior is critical
- Form logic in `src/forms/` (validation, submit behaviors, error states)
- Hooks in `src/hooks/` (query hooks, state hooks, derived state)
- Services in `src/services/` (request building, response handling, error branching)
- Auth and middleware helpers in `src/libs/` and `src/middleware.ts`

### 4.5 Out-of-Scope Testing Areas

- Full end-to-end flows that require backend + database + third-party integrations (covered by Cypress)
- Browser compatibility testing across many devices and browsers (covered by E2E strategy)
- Performance and load testing
- UI snapshot approval workflows for all components (not recommended for this codebase due to churn)

---

## 5. Testing Tools and Frameworks

### 5.1 Jest

Jest is the current unit test runner in the frontend project:

- Config: `jest.config.js`
- Scripts: `npm test`, `npm run test:watch`, `npm run test:coverage`
- Node version: see `.nvmrc` (18.16)
- Environment: `jsdom`
- Transform: `babel-jest` with `next/babel` preset
- Module alias: `@/` mapped to `src/`
- Setup files:
  - `jest.setup.js` (recommended place for `@testing-library/jest-dom` and shared mocks)
  - `src/test-setup.ts` (defines `global.jestExpect` to avoid conflicts with Cypress)

Practical notes:

- Run tests from the frontend project root (where `package.json` and `jest.config.js` live) to ensure module resolution behaves consistently.
- Component tests may require additional Jest mappers for CSS and static assets (see Phase 1).

### 5.2 React Testing Library

React Testing Library is the recommended approach for component and hook testing:

- Use `render`, `screen`, and queries by role/text/label to validate UI behavior.
- Prefer `renderHook` for hook testing, with providers in wrappers when needed.

### 5.3 Cypress (E2E Context)

Cypress exists in the frontend project for E2E. This proposal:

- Does not replace Cypress.
- Reduces the burden on Cypress by moving UI/logic edge cases to fast unit/component tests.
- Keeps E2E focused on critical user journeys, smoke tests, and integration confidence.

### 5.4 Mocking and Test Utilities

Recommended tools and patterns:

- Jest module mocks: `jest.mock()` for services, third-party SDKs, `next/navigation`, etc.
- Manual mocks under `src/__mocks__/` for common global or third-party stubs.
- Shared `renderWithProviders()` helper to wrap Query Client / Context providers for component tests.

Optional (proposal-level) additions that improve realism for API mocking:

- MSW (Mock Service Worker) for request-level mocking in component tests.
- `@testing-library/user-event` for higher-fidelity user interactions.

Any optional additions should be introduced only when there is a clear benefit and team agreement.

---

## 6. Component Testing Strategy

### 6.1 Responsibilities of UI Components

In this codebase, a typical UI component or block is responsible for:

- Rendering content given props/state.
- Handling user interactions (click, input, keyboard, selection).
- Displaying derived state (loading, empty, error).
- Triggering side effects through callbacks or hooks (navigation, service calls, toasts).

### 6.2 What Should Be Tested

Component tests should prioritize:

- Rendering logic (critical text, headings, labels, and semantics)
- Props behavior and callbacks
- Conditional UI (permissions, statuses, device variants such as mobile routes)
- User interactions that change UI state (open/close modal, validation errors, submit)
- Accessibility basics (roles, accessible labels, keyboard reachability where relevant)

### 6.3 What Should Not Be Tested

Avoid tests that:

- Assert implementation details (internal state variables, private helper calls) when behavior is sufficient.
- Assert Tailwind class names or layout pixel-perfect behavior.
- Duplicate E2E coverage for full flows inside a single unit test.
- Depend on network, real time, or real browser APIs without controlled mocks.

### 6.4 Testing Reusable Components (`src/components/`)

Priorities:

- Inputs and form primitives used widely across the app
- Common complex components (tables, pagination, document viewers, navigation)

Strategy:

- For each shared component, target the highest-risk behaviors and edge cases.
- Maintain stable selectors: prefer role/label; use `data-testid` only for non-semantic elements.

### 6.5 Testing Feature Components (`src/blocks/`)

Feature blocks often combine multiple concerns (hooks, forms, services). The recommendation:

- Test blocks at the user-behavior level (render + interactions).
- Mock service modules and query hooks at their boundary.
- Do not attempt to replicate E2E inside block tests; focus on local correctness.

### 6.6 Common Edge Cases

- Conditional rendering by role/permission
- Empty state vs loading vs error state
- Multiple async states (two parallel queries)
- Partial data presence (missing optional fields)
- Mobile-specific behavior under `src/app/mobile/`
- Clipboard, file uploads, and browser APIs (require explicit mocks)

---

## 7. Form Testing Strategy

### 7.1 React Hook Form Testing Scope

When testing RHF-driven forms:

- Validate default values and prefilled values.
- Validate required fields, error messages, and field-level constraints.
- Validate submit behavior (success path and failure path).

### 7.2 Zod Validation Testing

Zod validation should be tested at two levels:

- Pure schema unit tests (recommended for complex schemas).
- Form-level tests verifying that schema errors are shown to the user.

### 7.3 Form Submission and Error States

Test cases should include:

- Submit with valid data and assert service call and user feedback.
- Submit with invalid data and assert errors without service calls.
- Service error handling (API returns error status) and UI error state.

### 7.4 Testing Complex Forms (`src/forms/`)

Complex forms (multi-step, conditional, dynamic fields) should be tested incrementally:

- Unit test schema/transform helpers if they exist.
- Component test the most important steps and conditional branches.
- Keep tests focused: prefer a few high-value scenarios over exhaustive permutations.

---

## 8. Hooks Testing Strategy

### 8.1 Custom Hook Responsibilities

Custom hooks in `src/hooks/` typically:

- Fetch data via TanStack Query
- Derive state from props and global state
- Coordinate side effects (e.g., storage, navigation, toasts)

### 8.2 What Should Be Tested in Hooks

- Returned state shape and derived values
- Handling of loading/error states
- Behavior under parameter changes
- Proper calls to service modules and response handling

### 8.3 Testing Hooks with React Testing Library

Use `renderHook()` from `@testing-library/react` and wrap providers when required:

- Query Client wrapper for TanStack Query hooks
- Context providers for hooks reading from React Context

### 8.4 Mocking Dependencies in Hooks

Mock the boundary dependency:

- Mock service modules (Axios client wrappers) rather than mocking internal hook logic.
- Disable React Query retries in tests to avoid delays and flakiness.
- For date/time or randomness, inject dependencies or mock deterministically.

---

## 9. Service and API Layer Testing

### 9.1 Role of `src/services/`

`src/services/` contains:

- Request clients and interceptors (`baseRequest.ts`)
- API modules grouped by domain (`companies.service.ts`, `documents.service.ts`, etc.)
- Cookie/local storage helpers (`cookies.service.ts`, `storage.service.ts`)

The objective of service tests is to validate request/response behavior and error branching without network calls.

### 9.2 What Should Be Tested

- Correct request parameters (URL, method, payload, headers) for key calls
- Response mapping and transformation (when present)
- Error handling logic (401/403 branching, redirects, retry behavior)

### 9.3 Mocking API Calls

Recommended approaches:

- Mock Axios at the module boundary (mock `request` from `baseRequest.ts`).
- For deeper request behavior, consider a request mocking library (proposal-level optional).

Rules:

- No real network calls in unit/component tests.
- Avoid mocking deeply inside Axios; mock at the service module layer whenever possible.

### 9.4 Integration with React Query

For hooks built on TanStack Query:

- Unit test the hook’s behavior with a Query Client wrapper.
- Mock the underlying service call to return deterministic data.
- Validate correct states: loading, success, error.

---

## 10. State Management Testing

### 10.1 React Query Testing Scope

Test:

- Query hooks behavior (enabled/disabled conditions, refetch triggers where relevant)
- Data selection and transformation in hooks
- Error handling paths and fallback UI behavior (usually at component level)

### 10.2 Zustand Store Testing

Zustand usage exists in multiple parts of the codebase. Store tests should validate:

- Initial state
- State transitions for actions
- Reset/cleanup behavior between tests

Prefer testing store logic without React rendering when possible.

### 10.3 Context Provider Testing

For providers in `src/context/` and `src/providers/`:

- Unit test pure helper logic where possible.
- Component test provider + consumer behavior (initial state, updates, and clean-up).

### 10.4 State Isolation and Test Stability

Stability rules:

- Never share Query Clients between tests; create a fresh client per test.
- Reset Zustand stores between tests (or re-create the store per test file).
- Reset module mocks and global state in `beforeEach`.

---

## 11. Routing and Middleware Testing

### 11.1 App Router Testing Considerations

App Router routes live in `src/app/`. Testing guidance:

- Prefer component testing for client components.
- Avoid testing Next.js rendering internals. Treat route files as composition layers.
- If a route contains complex logic, extract it into a hook/service/helper and unit test it there.

### 11.2 Protected Routes Testing

Access control lives across middleware + auth helpers. Tests should cover:

- Navigation behavior when unauthenticated
- Role-based access and denial behavior
- Redirect logic for special states (account locked/suspended, expired password, 2FA requirements)

### 11.3 Middleware Logic Testing (`src/middleware.ts`)

`src/middleware.ts` is a high-value area for unit tests because regressions cause widespread access issues.

- Existing baseline test: `src/__tests__/middleware.test.ts`.
- Expand coverage by adding scenarios for each middleware function in `src/libs/middlewares/`.
- Keep middleware tests isolated by mocking cookie services, permission helpers, and Next.js response helpers.

### 11.4 Redirect and Access Control Scenarios

Core scenarios to include:

- Public routes that must bypass auth checks
- Auth routes that should redirect authenticated users away from login/setup pages
- Trial and affiliate route behaviors
- Mobile route redirection behavior
- 401 vs 403 distinctions (refresh token vs forbidden)

---

## 12. Mocking Strategy

### 12.1 Mocking API Services

Default approach:

- Mock service modules (`src/services/*`) and return deterministic data.

When needed:

- Mock `baseRequest.ts` and assert that service modules call it correctly.

### 12.2 Mocking Browser APIs

Common browser APIs used in the codebase include:

- `navigator.clipboard` and `ClipboardItem`
- `window.location` redirects
- `localStorage` and `sessionStorage`
- File APIs (upload inputs)

Guidelines:

- Implement small, reusable stubs for these APIs in shared test setup.
- Avoid coupling tests to real browser behavior.

### 12.3 Mocking Third-Party Libraries

Third-party integrations and UI libraries should be mocked at the smallest stable boundary:

- `next/navigation` and `next/router` (if used)
- `next/image` for image rendering
- Analytics and tracking SDKs (PostHog, Hotjar)
- Payment/identity SDKs (Recurly, Onfido, Sumsub)
- Toasting libraries (`react-toastify`)

### 12.4 Avoiding Over-Mocking

Over-mocking is a maintenance risk. Avoid:

- Mocking internal helpers that are part of the unit under test.
- Mocking React internals or hook implementations.

Instead:

- Mock only external boundaries (network, time, storage, third-party SDKs).
- Keep test assertions focused on behavior, not call counts on internal helpers.

---

## 13. Test Data Management

### 13.1 Test Fixtures

Use fixtures for:

- Static JSON-like test payloads (API responses, form defaults)
- Role/permission profiles
- Route params and navigation states

Recommendation:

- Store fixtures under `src/__tests__/fixtures/` and keep them small and focused.

### 13.2 Factory Functions

Factories are preferred for complex domain models:

- Build minimal valid objects and allow overrides.
- Keep factories deterministic (no random data unless seeded).

Recommendation:

- Create `src/__tests__/factories/` for objects such as `Company`, `AuthUser`, and common API responses.

### 13.3 Reusable Mock Data

Define reusable mock data only when it is:

- Used in multiple test suites
- Stable over time
- Meaningfully reduces duplication

### 13.4 Maintainable Test Data Design

Guidelines:

- Prefer minimal valid data over full production payloads.
- Avoid copying large responses directly into tests.
- Encode intent in the data shape: name key fields and ensure they support assertions clearly.

---

## 14. Folder Structure and Naming Conventions

### 14.1 Recommended Test Folder Structure

Recommended structure for tests within `src/`:

```
src/
  __tests__/
    app/
    blocks/
    components/
    forms/
    hooks/
    libs/
    services/
    utils/
    fixtures/
    factories/
    test-utils/
      renderWithProviders.tsx
```

Notes:

- Keep tests under `src/__tests__/` to avoid mixing production code and tests.
- Mirror production structure for discoverability (e.g., `components/` tests under `__tests__/components/`).

### 14.2 File Naming Standards

- Unit tests: `*.test.ts`
- Component tests: `*.test.tsx`
- Avoid mixing `*.spec.*` unless the team standardizes on it.

### 14.3 Test Case Naming Guidelines

- Use descriptive `describe()` blocks by module/component name.
- Use `it()` / `test()` names that describe behavior:
  - "should render loading state while fetching"
  - "should call onSubmit with valid payload"
  - "should redirect unauthenticated users to /login"

---

## 15. Code Coverage and Quality Metrics

### 15.1 Coverage Targets

Coverage should be introduced in phases to avoid blocking the team:

- Establish baseline coverage and enforce it does not regress.
- Require meaningful coverage for critical modules (`libs/auth`, `libs/middlewares`, `services/baseRequest`, shared `components/`).
- Encourage coverage for new/changed code paths via PR review standards.

Suggested phased targets:

- Phase 1: baseline measurement only (no strict gate), fix configuration gaps.
- Phase 2: introduce minimal thresholds for the whole project to prevent accidental removal of tests.
- Phase 3: tighten thresholds for critical folders and new modules.

### 15.2 Measuring Coverage with Jest

From the frontend project root:

- Run unit tests: `npm test`
- Run coverage: `npm run test:coverage`

Example for a specific file:

```
npm test -- src/__tests__/middleware.test.ts --coverage --collectCoverageFrom=src/middleware.ts
```

### 15.3 Interpreting Coverage Reports

Use coverage to identify:

- High-risk areas with low test coverage (auth, middleware, services)
- Modules where test investment reduces regressions significantly

Avoid using coverage as a single quality metric; stable, meaningful tests are the priority.

### 15.4 Handling Low Coverage Areas

- Do not attempt to cover everything immediately in a large UI codebase.
- Prioritize high-risk and frequently changing areas.
- Refactor to make code testable (extract pure functions, reduce hard dependencies on `window`/`document`).

---

## 16. CI/CD Integration

### 16.1 Running Frontend Tests in CI

Recommended PR pipeline steps for the frontend project:

- Install dependencies (`npm ci` when lockfile is stable)
- Run typecheck (`npm run ts-check`)
- Run unit/component tests (`npm test`)
- Optionally publish coverage artifacts for review

### 16.2 Build Fail Conditions

Fail the build if:

- Any unit/component test fails
- Typecheck fails

### 16.3 Coverage Enforcement

Introduce coverage enforcement gradually:

- Start with minimal thresholds to prevent regressions.
- Increase thresholds only when the test suite is stable and developers can maintain it without slowing delivery.

### 16.4 Test Performance Considerations

- Keep unit/component tests fast (target < 5 minutes in CI).
- Disable retries in query hooks for tests.
- Avoid running Cypress (E2E) on every PR if it becomes slow; use a separate schedule or tag-based strategy.

---

## 17. Best Practices and Guidelines

### 17.1 Writing Maintainable Frontend Tests

- Write tests that read like user behavior and expected outcomes.
- Prefer stable assertions and avoid implementation coupling.
- Keep each test small and focused.

### 17.2 Arrange–Act–Assert Pattern

Use a consistent structure:

- Arrange: set up test data, mocks, and render
- Act: perform user interactions or invoke logic
- Assert: validate output, UI, or side effects

### 17.3 Happy Path vs Edge Case Testing

For each module/component:

- Cover the happy path first (core expected behavior).
- Add a small number of high-impact edge cases (permissions, empty/error states).
- Leave exhaustive permutations to E2E only when necessary.

### 17.4 Keeping Tests Fast and Deterministic

- No real network calls.
- Avoid timers; if needed, use fake timers deterministically.
- Reset mocks and state between tests.
- Avoid snapshot overuse; prefer explicit assertions.

---

## 18. Risks and Mitigation

### 18.1 Large Codebase Complexity

Risk:

- Adding tests without structure may create inconsistent patterns and high maintenance cost.

Mitigation:

- Standardize folder structure and test utilities early.
- Provide review guidelines and examples.

### 18.2 Flaky UI Tests

Risk:

- Async data fetching, timers, and browser API differences can lead to flaky tests.

Mitigation:

- Disable retries in query tests.
- Mock browser APIs explicitly (clipboard, location, storage).
- Prefer unit tests for pure logic and keep component tests focused.

### 18.3 Test Maintenance Cost

Risk:

- UI changes may break tests frequently if tests are too coupled to markup.

Mitigation:

- Query by role/label/text rather than DOM structure.
- Test behavior rather than implementation details.

### 18.4 Mitigation Strategies

- Establish a shared `renderWithProviders` helper.
- Add a documented mocking strategy for common dependencies.
- Keep a small "golden set" of critical tests that must remain stable.

---

## 19. Implementation Plan

### 19.1 Phase 1: Test Environment Setup

- Confirm Jest + RTL baseline in the frontend project (config, scripts, path aliases).
- Ensure Jest supports CSS imports and CSS Modules used across the codebase (e.g., `*.module.css`, third-party library CSS).
- Ensure Jest supports non-code imports where relevant (images, fonts, SVGs).
- Enable `@testing-library/jest-dom` in `jest.setup.js` for consistent DOM assertions.
- Add shared test utilities (providers, common mocks).
- Document and standardize mocking patterns for Next.js modules and browser APIs.

### 19.2 Phase 2: Core Component and Hook Tests

- Cover shared components that are widely used (inputs, tables, modals).
- Cover core hooks that manage data fetching and derive critical state.
- Ensure Query hooks have stable test wrappers and disabled retries.

### 19.3 Phase 3: Form and Service Tests

- Add tests for high-risk forms (auth flows, onboarding, document actions).
- Add service tests for error branching and request/response mapping.

### 19.4 Phase 4: Middleware and Routing Tests

- Expand middleware unit tests for all major redirect and access-control scenarios.
- Add tests for route-level composition only when logic cannot be extracted.

### 19.5 Phase 5: CI Integration and Review

- Integrate unit/component tests into CI.
- Introduce incremental coverage thresholds.
- Review and optimize test speed and reliability.

---

## 20. Estimated Effort and Timeline

### 20.1 Effort Estimation by Feature Area

Estimates depend on scope and quality requirements. A practical starting point:

- Phase 1 setup: 1–3 engineer-days
- Shared components (initial set): 5–10 engineer-days
- Hooks and services (critical set): 5–10 engineer-days
- Forms (critical flows): 5–15 engineer-days
- Middleware and access control: 2–5 engineer-days
- CI integration and stabilization: 2–5 engineer-days

### 20.2 Timeline Overview

With 1 engineer (part time) or 2 engineers (shared), a phased rollout can be completed over 3–6 weeks while still supporting feature delivery.

### 20.3 Resource Allocation

- Frontend engineers: write and maintain unit/component tests alongside features.
- Tech lead: define standards, review patterns, and prioritize critical areas.
- DevOps: integrate tests and coverage into CI and ensure pipeline stability.

---

## 21. Conclusion

### 21.1 Summary of Benefits

Implementing this proposal will:

- Reduce UI regressions in core flows
- Increase confidence in refactoring and releases
- Provide fast feedback in CI and improve developer velocity
- Complement existing Cypress E2E testing with targeted, maintainable coverage

### 21.2 Expected Outcomes

- A standardized unit/component testing structure for the frontend project
- Reliable tests for middleware, auth/permission logic, and key UI modules
- A stable foundation to expand coverage incrementally

### 21.3 Next Steps

- Align on folder structure and test utility patterns.
- Implement Phase 1 setup tasks and confirm baseline stability.
- Choose a small set of high-value components/hooks to cover first.

---

## 22. Appendix

### 22.1 Sample Component Test

The following is an illustrative example of a component test pattern using React Testing Library. This example demonstrates mocking a side-effect boundary (toasts) and a browser API (clipboard).

```ts
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import { toast } from "react-toastify";

jest.mock("react-toastify", () => ({
  toast: { success: jest.fn(), error: jest.fn() }
}));

describe("Copy to clipboard behavior", () => {
  beforeEach(() => {
    (toast.success as jest.Mock).mockClear();
    (toast.error as jest.Mock).mockClear();
    Object.assign(navigator, {
      clipboard: { writeText: jest.fn().mockResolvedValue(undefined) }
    });
  });

  it("should copy text and show success message", async () => {
    const Component = ({ value }: { value: string }) => (
      <button
        onClick={async () => {
          await navigator.clipboard.writeText(value);
          toast.success("Copied");
        }}
      >
        Copy
      </button>
    );

    render(<Component value="ABC" />);
    fireEvent.click(screen.getByRole("button", { name: "Copy" }));

    expect(navigator.clipboard.writeText).toHaveBeenCalledWith("ABC");
    await waitFor(() => expect(toast.success).toHaveBeenCalledWith("Copied"));
  });
});
```

### 22.2 Sample Hook Test

An example hook test pattern for TanStack Query hooks using a Query Client wrapper:

```ts
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { renderHook, waitFor } from "@testing-library/react";
import { useQuery } from "@tanstack/react-query";

function createTestQueryClient() {
  return new QueryClient({
    defaultOptions: { queries: { retry: false } }
  });
}

describe("Example query hook", () => {
  it("should return data when service resolves", async () => {
    const queryClient = createTestQueryClient();

    const wrapper = ({ children }: { children: React.ReactNode }) => (
      <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
    );

    const fetcher = jest.fn().mockResolvedValue({ id: "1" });
    const useExample = () =>
      useQuery({ queryKey: ["example"], queryFn: fetcher });

    const { result } = renderHook(() => useExample(), { wrapper });
    await waitFor(() => expect(result.current.isSuccess).toBe(true));

    expect(result.current.data).toEqual({ id: "1" });
    expect(fetcher).toHaveBeenCalledTimes(1);
  });
});
```

### 22.3 Jest and RTL Configuration Examples

Current Jest configuration (source of truth: `jest.config.js`):

```js
module.exports = {
  testEnvironment: "jsdom",
  setupFilesAfterEnv: [
    "<rootDir>/jest.setup.js",
    "<rootDir>/src/test-setup.ts"
  ],
  testPathIgnorePatterns: [
    "<rootDir>/node_modules/",
    "<rootDir>/.next/",
    "<rootDir>/cypress/"
  ],
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/src/$1"
  },
  transform: {
    "^.+\\.(ts|tsx)$": ["babel-jest", { presets: ["next/babel"] }]
  }
};
```

Recommended additions when expanding component tests:

- Enable `@testing-library/jest-dom` in `jest.setup.js`:

```js
import "@testing-library/jest-dom";
```

- Map CSS and static assets to test stubs to avoid import errors from `*.css` / `*.module.css` and third-party CSS:

```js
moduleNameMapper: {
  "^@/(.*)$": "<rootDir>/src/$1",
  "\\\\.(css|less|scss)$": "<rootDir>/src/__tests__/test-utils/styleMock.js",
  "\\\\.(png|jpg|jpeg|gif|webp|avif|svg)$": "<rootDir>/src/__tests__/test-utils/fileMock.js"
}
```

- Mock Next.js-specific modules in `jest.setup.js` (as needed for the tested components):
  - `next/image` (render as a plain `img`)
  - `next/navigation` (mock `useRouter`, `usePathname`, `useSearchParams`)
