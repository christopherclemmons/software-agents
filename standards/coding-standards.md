# Coding Standards


## Purpose
Define consistent coding practices across all services to ensure readability, maintainability, and long-term scalability.


---


## General Principles


- Write code for humans first, machines second
- Prefer clarity over cleverness
- Avoid unnecessary abstraction
- Keep logic explicit and predictable
- Match the existing codebase style before introducing new patterns


---


## Naming Conventions


- Use clear, descriptive names
- Avoid abbreviations unless universally understood
- Use consistent casing per language:
  - camelCase → variables/functions
  - PascalCase → classes/types
  - UPPER_CASE → constants (when applicable)


Bad:

```ts
const usr = getUsr();
const calc = (x, y) => x + y;
```

Good:

```ts
const user = getUser();
const calculateTotal = (subtotal, tax) => subtotal + tax;
```


Additional guidelines:

- Names should reveal intent
- Prefer domain language over generic terms like `data`, `item`, or `stuff`
- Boolean names should read like flags, e.g. `isActive`, `hasAccess`, `canRetry`
- Function names should describe an action, e.g. `createInvoice`, `sendReminderEmail`
- Collections should use plural names, e.g. `users`, `orders`, `permissions`


---


## Functions


- Keep functions small and focused on one responsibility
- Prefer explicit inputs and outputs
- Avoid hidden side effects
- Use early returns to reduce nesting when it improves readability
- If a function needs a long comment to explain what it does, simplify the function first


Bad:

```ts
function handle(data: any) {
  if (data) {
    if (data.user) {
      if (data.user.isActive) {
        process(data.user);
      }
    }
  }
}
```

Good:

```ts
function handleActiveUser(user: User | null) {
  if (!user) return;
  if (!user.isActive) return;

  processUser(user);
}
```


---


## Variables


- Prefer `const` by default
- Use `let` only when reassignment is necessary
- Avoid `var`
- Keep variable scope as small as possible
- Do not reuse a variable for multiple meanings in the same function


Bad:

```ts
let data = fetchUser();
data = transformUser(data);
data = saveUser(data);
```

Good:

```ts
const user = fetchUser();
const transformedUser = transformUser(user);
const savedUser = saveUser(transformedUser);
```


---


## Conditionals


- Keep conditionals simple and readable
- Extract complex logic into well-named helper functions
- Avoid double negatives
- Prefer positive condition names where possible


Bad:

```ts
if (!user || !user.isDisabled === false) {
  allowAccess();
}
```

Good:

```ts
if (user && user.isEnabled) {
  allowAccess();
}
```


---


## Comments


- Write code that is clear enough to reduce the need for comments
- Use comments to explain why, not what
- Remove outdated comments immediately
- Do not restate obvious code behavior


Bad:

```ts
// Increment i by 1
i++;
```

Good:

```ts
// Retry once because the upstream API occasionally fails on the first request
retryRequest();
```


---


## Error Handling


- Fail loudly and clearly
- Never swallow errors silently
- Include useful context in logs and error messages
- Return consistent error shapes across services
- Prefer structured errors over raw strings when possible


Bad:

```ts
try {
  runJob();
} catch (error) {}
```

Good:

```ts
try {
  runJob();
} catch (error) {
  logger.error("Failed to run billing job", { error });
  throw error;
}
```


---


## Logging


- Log meaningful events, not noise
- Use structured logging where possible
- Never log secrets, tokens, passwords, or sensitive personal data
- Include identifiers useful for tracing, e.g. `userId`, `jobId`, `requestId`
- Use log levels consistently:
  - `debug` → development detail
  - `info` → normal lifecycle events
  - `warn` → unexpected but recoverable issues
  - `error` → failures requiring attention


---


## Formatting


- Use the formatter and linter configured for the project
- Do not manually fight automated formatting rules
- Keep line length reasonable based on project conventions
- Use consistent indentation, spacing, and import ordering
- Submit code that passes linting before review


---


## Types


- Prefer explicit types at service boundaries
- Avoid `any` unless there is a documented reason
- Model domain concepts with named types/interfaces
- Keep shared types small and purposeful
- Validate external input at runtime, even in typed languages


Bad:

```ts
function createUser(payload: any) {
  return save(payload);
}
```

Good:

```ts
interface CreateUserInput {
  email: string;
  name: string;
}

function createUser(payload: CreateUserInput) {
  return saveUser(payload);
}
```


---


## API and Service Boundaries


- Keep request and response shapes explicit
- Validate all external input
- Do not leak internal implementation details through public APIs
- Prefer backward-compatible changes when evolving contracts
- Document breaking changes clearly


---


## File Organization


- Keep files focused and reasonably small
- Group related logic together
- Avoid large “utility” files with unrelated helpers
- Name files after the primary responsibility they contain
- Prefer predictable folder structures across services


---


## Testing


- Write tests for behavior, not implementation details
- Cover critical paths, edge cases, and failure cases
- Keep tests readable and maintainable
- Use descriptive test names
- Fix flaky tests instead of ignoring them


Bad:

```ts
it("works", () => {
  ...
});
```

Good:

```ts
it("returns a 403 when the user does not have permission", () => {
  ...
});
```


---


## Code Reviews


- Optimize for constructive, respectful feedback
- Review for correctness, readability, maintainability, and consistency
- Prefer small pull requests when possible
- Do not approve code you do not understand
- Treat review comments as collaboration, not criticism


---


## Security


- Never hardcode secrets or credentials
- Use environment variables or approved secret managers
- Sanitize and validate all untrusted input
- Apply least-privilege access by default
- Be careful with logs, exports, and third-party integrations


---


## Final Standard


When making a coding decision, choose the option that makes the code easier for the next engineer to understand, change, and trust.