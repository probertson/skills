### Additional guidelines that are specific to backend code

These examples assume a NestJS + TypeScript service. Adapt the specifics to your stack; the intent behind each rule generalizes.

#### What To Look For

1. **Test quality**: Review the provided test code for quality, correctness, and reliability. Focus on whether tests validate real behavior rather than superficial checks.
   - Test matches its title
     - Ensure the assertions actually verify what the test name describes.
     - Bad: Test says "calls `foo()` with correct args" but only checks `toHaveBeenCalled()`.
     - Good: Verifies exact arguments: `toHaveBeenCalledWith(...)`.
   - Side effects are verified
     - Confirm that important side effects (DB writes, API calls, state changes) are asserted.
     - Bad: Only checks return value when function is supposed to persist data.
     - Good: Asserts side effect: `expect(db.save).toHaveBeenCalledWith(...)`.
   - Mocks are used correctly
     - Avoid tests that only prove mocks were called without validating logic.
     - Bad: Heavy mocking with assertions only on mock calls.
     - Good: Validates output/behavior; mocks only external dependencies.
   - Edge cases are covered
     - Ensure tests include non-happy paths.
     - Bad: Only tests valid input.
     - Good: Includes `null`, empty, boundary, and invalid inputs.
2. **API Client types**: Verify that the type definition and validator rules specified in the DTO (or equivalent) match the actual values that are written to or read from those objects. When these type definitions are the source for API documentation (e.g. Swagger) and a generated api-client used by front-end code, the types must match in order for the front-end type checking to be valid.
   - In a class defining the shape of a function's return, a property marked required (e.g. `@ApiProperty({ required: true })` and without `@IsOptional()`) must never be set to `undefined` (even implicitly by omission) by the code that constructs the return object. Similarly, unless the type and validators specify that `null` is allowed, the value should never be set to `null`.
3. **Unhandled promise rejections**: Flag `async` functions without `try...catch` blocks or `.catch()` handlers. Unhandled rejections can crash Node.js processes or leave applications in inconsistent states.
4. **API error responses**: Verify proper HTTP status codes (4xx for client errors, 5xx for server errors) and consistent error response formats across endpoints.
5. **Logging sensitive data**: Flag potential PII (emails, names, addresses) or secrets (tokens, passwords, API keys) in log statements. These violate privacy and security requirements.
6. **N+1 query problems**: Flag database queries inside loops. These cause severe performance degradation as data grows. Use batch queries or joins instead.
7. **Missing input validation**: Flag unvalidated inputs from external sources (`req.body`, query params, headers). All external input should be validated before use to prevent injection attacks and malformed data bugs.
