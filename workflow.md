# Simple Workflow Ideation

## High level

Work vertically, not horizontally.

Treat each implementation step as a thin end-to-end behavior slice that proves part of the final intent works from caller/UI/API entrypoint down through the necessary domain/service/data boundary and back.

Do not build entire layers in isolation.

Bad:
- create all DB models
- then create all services
- then create all API routes
- then wire frontend
- then write tests

Good:
- choose one narrow user/business behavior
- write or update the acceptance/functional test that describes it
- implement only the minimum frontend/API/service/db/domain changes needed for that behavior
- wire the full path
- stub/mock only the parts outside this slice
- verify the slice works
- then repeat with the next behavior

Before coding, produce a vertical slice plan with:
1. The final intent in one sentence.
2. The smallest meaningful behavior slice.
3. The entrypoint that exercises it.
4. The domain/data path it must touch.
5. The test that proves it works.
6. What will be stubbed or deferred.
7. What not to build yet.

During implementation:
- Prefer one working path over many incomplete abstractions.
- Do not create broad generic infrastructure unless the current slice needs it.
- Do not add unused interfaces, adapters, services, schemas, routes, mocks, config, or abstractions “for later.”
- Every new file/function should be justified by the current vertical slice.
- Keep placeholder code local and explicit.
- If a dependency is not needed to prove the current behavior, stub it or defer it.
- If you notice yourself editing only one architectural layer for more than one step, stop and re-slice.

After each slice:
- Run the most relevant test.
- State what behavior now works end-to-end.
- State what remains intentionally incomplete.
- Propose the next smallest vertical slice.

## Personalized
For this task, implement by intent slices.

A slice is only complete when a real caller can exercise the behavior through the intended public boundary, not when an internal layer compiles.

Use this loop:

1. Select the smallest behavior that advances the requested outcome.
2. Add or update a functional/e2e/contract test describing that behavior.
3. Implement the minimum code across boundaries needed to make that test pass.
4. Use mocks/stubs only for collaborators outside this behavior.
5. Avoid broad refactors unless they remove duplication or complexity inside the current slice.
6. Stop after the slice is green and summarize the next slice.

Do not:
- build all types first
- build all services first
- build all API routes first
- create speculative abstractions
- migrate unrelated code
- write exhaustive mocks for future paths
- implement happy-path plus all variants before wiring the first path

Bias toward a narrow, real, wired path with visible behavior.
