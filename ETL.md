# ETL Story Playbook

Use this playbook when the active story involves ETL, data migration, backfill, sync jobs, imports, exports, reconciliation, or other data pipeline work.

## Goals

- Make the data flow testable end to end
- Validate behavior against a real test database when possible
- Prefer deterministic generated test data over ad hoc manual checks
- Prove idempotency, mapping correctness, and failure handling before marking the story done

## Required Workflow

1. Restate the pipeline scope
   - Identify source, transform rules, destination, scheduling trigger, and rollback surface
   - Note whether the story is one-time backfill, repeatable ETL job, or incremental sync

2. Inspect the current validation path
   - Find existing ETL tests, fixtures, SQL validation scripts, or reconciliation jobs
   - Reuse existing helpers before creating new ones

3. Prefer MCP-backed test database validation
   - If an MCP connection to a test database is available, use it for setup and verification
   - If MCP is unavailable, use the best local automated alternative and record that manual DB verification is still needed

4. Generate deterministic test data
   - Create representative source rows covering normal cases, edge cases, duplicates, nulls, and malformed records where relevant
   - Keep generated data reproducible with fixed values or a fixed seed
   - Save reusable fixtures or helper scripts if the codebase has a suitable place for them

5. Run the ETL flow
   - Execute the job, command, task, script, or function exactly as production code would
   - Avoid validating only internal helper functions if the real entrypoint can be exercised

6. Verify the outcome
   - Confirm inserted and updated row counts
   - Confirm field mappings and transformed values
   - Confirm duplicate handling and idempotency by rerunning when applicable
   - Confirm invalid rows are skipped, quarantined, or logged as required
   - Confirm downstream derived tables, aggregates, or audit records if the story touches them

7. Record the verification method
   - Keep the exact verification command, SQL, or test entrypoint in the progress log
   - If you add reusable checks, mention them in AGENTS.md when they are generally useful

## Acceptance Criteria Guidance

For ETL stories, prefer acceptance criteria like:

- "Generate deterministic fixture data covering valid and invalid source records"
- "Run the ETL entrypoint against the test database"
- "Target table row counts and mapped fields match expected results"
- "Rerunning the ETL does not create duplicates"
- "Invalid records are logged or rejected as specified"
- "Tests pass"

Avoid vague criteria like:

- "ETL works correctly"
- "Data looks right"

## Progress Log Expectations

When completing an ETL story, include:

- Data generation method
- ETL entrypoint used
- Verification query, script, or test command
- Whether MCP test database validation was performed
- Any remaining manual validation required
