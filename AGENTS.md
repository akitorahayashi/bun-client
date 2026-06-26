# AGENTS.md

## Directory Structure

```text
src/
  index.ts            Public export boundary
  client.ts           Public API client facade
  transport.ts        Fetch-based HTTP boundary
  errors.ts           Client, HTTP, and response parse errors
  types/
    <resource>.ts     API request and response DTOs
tests/
  client.test.ts      Tests for src/client.ts
  transport.test.ts   Tests for src/transport.ts
```

## Architecture

### Public API

`src/index.ts` owns the package export surface. Public functions, classes, and types are re-exported from this file. Internal modules are not imported directly by consumers.

### Client

`client.ts` owns the user-facing API client class. The client maps high-level methods to typed transport requests and returns typed response models.

### Transport

`transport.ts` owns URL construction, query serialization, JSON request bodies, response parsing, and HTTP failure handling. HTTP failures and response parse failures are explicit errors.

### Types

`src/types/` owns API request and response DTOs. DTO files are named by resource or endpoint family, not by generic labels.

### Errors

`errors.ts` exports `ClientError` as the shared client error base. `HttpError` represents non-2xx HTTP responses, and `ResponseParseError` represents invalid or unexpected response bodies.

### Test Mapping

Each test file corresponds to one implementation file in `src/`. Test names are
derived from the implementation file they cover rather than from cross-cutting
concepts such as `api`, `validation`, or `integration`.

Files under `src/types/` map to `tests/types/` with the same basename only when
they own runtime behavior, parsers, or validation. DTO-only files do not
require placeholder test files.

## Development Commands

```sh
bun run fix      # Biome autofix
bun run check    # Biome lint + tsc --noEmit
bun test         # Run all tests
```

## Development Guidelines

- `bun run fix` runs before `bun run check`.
- Tests assert public behavior through exports from `src/index.ts`.
- Test filenames correspond to implementation filenames so the owning test file
  is discoverable from the edited source file.
- Client tests use injected fetch implementations or local test servers. External service availability is not required.
- New client methods include tests at the package boundary.
- Cross-cutting test filenames such as `api.test.ts`, `validation.test.ts`, and
  `integration.test.ts` are not used for unit-level ownership.
- Package consumers import from the package root, not from nested source files.
- The package is consumed as Bun-readable TypeScript from GitHub URL dependencies. No `dist/` build output is required.

## Documentation Rules

Documentation is written in a declarative style describing the current state of the system. Imperative or changelog-style descriptions are not used.
