# E2E Testing Quick Start Guide

## SpeedyDD - Cypress Testing

Quick guide to get started with E2E testing for SpeedyDD.

---

## 📋 Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Running Tests](#running-tests)
4. [Writing New Tests](#writing-new-tests)
5. [Best Practices](#best-practices)
6. [Troubleshooting](#troubleshooting)

---

## Requirements

- Node.js 18+
- npm or yarn
- Next.js dev server running at `http://localhost:3000`
- Backend API running at `http://localhost:8080` (or configured in `.env.test`)

---

## Installation

### 1. Install Dependencies

```bash
npm install
```

### 2. Setup Environment Variables

Configure environment variables in `cypress.config.ts`:

```typescript
export default defineConfig({
  e2e: {
    baseUrl: process.env.CYPRESS_BASE_URL || "http://localhost:3000",
    env: {
      app_route: process.env.CYPRESS_APP_ROUTE || "http://localhost:3000",
      api_url: process.env.CYPRESS_API_URL || "http://localhost:8080"
    }
  }
});
```

Create `.env.test` file to set environment variables:

```env
CYPRESS_BASE_URL=http://localhost:3000
CYPRESS_APP_ROUTE=http://localhost:3000
CYPRESS_API_URL=http://localhost:8080
```

**Note:** Cypress doesn't automatically load `.env.test`. Use `dotenv` package or set as system environment variables.

### 3. Verify Cypress Installation

```bash
npx cypress verify
```

---

## Running Tests

### Run Tests in Interactive Mode (Recommended for Development)

```bash
# Terminal 1: Start Next.js dev server
npm run dev

# Terminal 2: Open Cypress
npm run cypress
# Or: npx cypress open
```

Cypress Test Runner will open, select test file to run.

### Run Tests in Headless Mode (CI/CD)

```bash
# Start Next.js server
npm run build && npm start &

# Wait for server
npx wait-on http://localhost:3000

# Run all tests
npx cypress run

# Run specific test file
npx cypress run --spec "cypress/e2e/auth/login.cy.ts"

# Run tests with specific tags
npx cypress run --env grepTags=@smoke
```

### Run Tests with Tags

```bash
# Run only smoke tests
npx cypress run --env grepTags=@smoke

# Run auth tests
npx cypress run --env grepTags=@auth

# Run multiple tags
npx cypress run --env grepTags="@smoke|@auth"
```

---

## Writing New Tests

### 1. Create Test File

Create a new file in `cypress/e2e/` following feature structure:

```typescript
// cypress/e2e/feature-name/test-name.cy.ts
describe("Feature Name", { tags: ["@smoke"] }, () => {
  beforeEach(() => {
    // Setup: login, navigate, etc.
    cy.login("admin@speedydd.test", "Admin123!");
  });

  it("should do something", () => {
    // Arrange
    cy.visit("/lobby/some-page");

    // Act
    cy.get('[data-testid="button"]').click();

    // Assert
    cy.get('[data-testid="result"]').should("be.visible");
  });
});
```

### 2. Use Custom Commands

See available custom commands in `cypress/support/commands.ts`:

```typescript
// Login
cy.login("email@example.com", "password123");

// Logout
cy.logout();

// Create company (if implemented)
cy.createCompany({ name: "Test Company" });
```

### 3. Use Fixtures

Load test data from fixtures:

```typescript
cy.fixture("auth/users").then((users) => {
  cy.login(users.admin.email, users.admin.password);
});
```

### 4. Add Test Data

Add test data to `cypress/fixtures/`:

```json
// cypress/fixtures/feature/data.json
{
  "item1": { "name": "Test Item 1" },
  "item2": { "name": "Test Item 2" }
}
```

---

## Best Practices

### 1. Use data-testid

Instead of CSS selectors, use `data-testid`:

```typescript
// ❌ Bad
cy.get(".btn-primary").click();

// ✅ Good
cy.get('[data-testid="submit-button"]').click();
```

### 2. AAA Pattern (Arrange-Act-Assert)

```typescript
it("should do something", () => {
  // Arrange: Setup test data, navigate to page
  cy.visit("/lobby/page");
  cy.fixture("data").then((data) => {
    // Act: Perform actions
    cy.get('[data-testid="input"]').type(data.value);
    cy.get('[data-testid="submit"]').click();

    // Assert: Verify results
    cy.get('[data-testid="result"]').should("contain", data.expected);
  });
});
```

### 3. Independent Tests

Each test must be independent, not dependent on other tests:

```typescript
// ❌ Bad: Test depends on previous test
it("should create item", () => {
  cy.createItem();
});

it("should edit item", () => {
  // Assumes item was created in previous test
  cy.editItem();
});

// ✅ Good: Each test is independent
it("should create item", () => {
  cy.createItem();
  cy.get('[data-testid="item"]').should("be.visible");
});

it("should edit item", () => {
  cy.createItem(); // Create in this test
  cy.editItem();
  cy.get('[data-testid="item"]').should("contain", "edited");
});
```

### 4. Clear Test Names

Test names must clearly describe what they test:

```typescript
// ❌ Bad
it('test 1', () => { ... });

// ✅ Good
it('should login successfully with valid credentials', () => { ... });
it('should show error message with invalid credentials', () => { ... });
```

### 5. Use Tags

Tag tests for easy filtering:

```typescript
describe("Login Flow", { tags: ["@smoke", "@auth"] }, () => {
  it("should login", { tags: ["@critical"] }, () => {
    // Critical test
  });

  it("should logout", () => {
    // Regular test
  });
});
```

---

## Troubleshooting

### Tests Fail with "Element not found"

**Cause:** Element hasn't rendered yet or selector is wrong.

**Solution:**

- Add wait: `cy.wait(1000)` (not recommended)
- Use Cypress auto-waiting: `cy.get('[data-testid="element"]').should('be.visible')`
- Check selector in Cypress Test Runner

### Tests Fail with "Network error"

**Cause:** Backend API is not running or network issue.

**Solution:**

- Verify backend is running: `curl http://localhost:8080/api/health`
- Check environment variables in `.env.test`
- Check network tab in Cypress

### Tests Are Flaky (Sometimes Pass, Sometimes Fail)

**Cause:** Race conditions, timing issues.

**Solution:**

- Add explicit waits: `cy.get('[data-testid="element"]').should('be.visible')`
- Use `cy.intercept()` to wait for API calls
- Review test logic, may need refactoring

### Cypress Won't Open

**Cause:** Port conflict or Cypress not installed.

**Solution:**

```bash
# Reinstall Cypress
npm install cypress --save-dev

# Clear Cypress cache
rm -rf ~/.cache/Cypress

# Check port
lsof -i :3000
```

### Videos/Screenshots Not Created

**Cause:** Config incorrect or disk space issue.

**Solution:**

- Check `cypress.config.ts`:
  ```typescript
  video: true,
  screenshotOnRunFailure: true
  ```
- Check disk space: `df -h`
- Check permissions for `cypress/videos/` and `cypress/screenshots/`

---

## References

- [Full E2E Testing Proposal](./E2E_TESTING_PROPOSAL.md)
- [Cypress Documentation](https://docs.cypress.io/)
- [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Next.js Testing Guide](https://nextjs.org/docs/testing)

---

## Support

If you encounter issues:

1. Check [Troubleshooting](#troubleshooting) section
2. Review [Full Proposal](./E2E_TESTING_PROPOSAL.md)
3. Check Cypress logs in Test Runner
4. Contact QA team

---

**Last Updated:** December 2025
