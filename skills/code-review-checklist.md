# Code Review Checklist Skill

## Purpose
This skill provides a comprehensive checklist for reviewing Python and SQL code against team standards, ensuring code quality, documentation completeness, and project maintainability.

## When to Use This Skill
* Before submitting code for peer review
* During code review sessions
* After completing a major coding task
* Before merging code to main/production branches
* When refactoring existing code

---

## Python Code Review Checklist

### Code Structure & Style

- [ ] **Naming Conventions**
  - Functions and variables use `snake_case`
  - Classes use `PascalCase`
  - Constants use `UPPER_CASE`
  - Boolean variables use `is_`, `has_`, or `can_` prefixes
  - Names are descriptive and self-documenting (avoid abbreviations except standard ones)

- [ ] **Type Hints**
  - All function arguments have type hints
  - All function return values have type hints
  - Complex types use proper typing imports (Dict, List, Optional, etc.)

- [ ] **Code Formatting**
  - Code follows PEP 8 conventions
  - Consistent indentation (4 spaces)
  - Code has been formatted with Databricks integrated formatter
  - Line length is reasonable (avoid extremely long lines)
  - Blank lines are truly blank (no trailing spaces or tabs)

- [ ] **Comment Placement**
  - Comments on their own line before the statement (not inline at end of lines)
  - Multi-line comment blocks at top of code sections with blank line before code
  - Comments explain "why" not "what"

### Documentation

- [ ] **Function Docstrings**
  - All functions have Google-style docstrings
  - Docstring includes: brief description, detailed explanation (if needed), Args, Returns, Raises
  - Examples provided for complex functions

- [ ] **Class Docstrings**
  - Class docstring placed above `__init__` method
  - Describes class purpose and responsibilities
  - Lists class attributes
  - `__init__` method has its own docstring with Args

- [ ] **Comments**
  - Logical sections have descriptive comments explaining intent
  - Complex logic has explanatory comments
  - No commented-out code blocks left in final version

### Import Organization

- [ ] **PEP 8 Import Order**
  - Standard library imports first (sorted alphabetically)
  - Third-party imports second (sorted alphabetically)
  - Local application imports third (sorted alphabetically)
  - Each group separated by a blank line

- [ ] **Multi-line Import Sorting**
  - Names within multi-line imports sorted alphabetically
  - Example: `ArrayType, FloatType, IntegerType` (not `IntegerType, ArrayType, FloatType`)

### Error Handling

- [ ] **Exception Handling**
  - No bare `except:` clauses
  - Specific exception types are caught
  - Error messages are informative and actionable
  - Errors logged with context (what failed, relevant values)
  - Troubleshooting hints included when possible

- [ ] **Input Validation**
  - Function inputs validated early
  - Edge cases handled (None, empty lists, invalid types)
  - Appropriate exceptions raised for invalid inputs

### Best Practices

- [ ] **DRY Principle**
  - No repeated code blocks (extracted into functions)
  - Common patterns abstracted into utilities

- [ ] **Code Complexity**
  - Functions are focused and single-purpose
  - Nesting depth kept reasonable (max 3-4 levels)
  - No magic numbers or strings (use named constants)

- [ ] **String Formatting**
  - F-strings used for string formatting (not % or .format())

- [ ] **Spark Usage**
  - DataFrame API preferred over RDDs
  - Avoid `.collect()` on large datasets
  - Broadcast joins used where appropriate

### Testing

- [ ] **Unit Tests**
  - Unit tests exist for all business logic functions
  - Tests cover edge cases (empty inputs, None values, boundaries)
  - Test names are descriptive
  - External dependencies are mocked

### Security

- [ ] **Credentials & Secrets**
  - No hardcoded credentials, tokens, or API keys
  - Secrets use Databricks secrets or environment variables
  - No sensitive data in logs or print statements

- [ ] **PII Handling**
  - PII columns not unnecessarily printed or sampled
  - Appropriate access controls verified

---

## SQL Code Review Checklist

### Query Organization

- [ ] **Temp View Strategy**
  - Complex queries broken into logical temp views
  - Each temp view represents one clear transformation
  - Avoids monolithic queries doing too much

- [ ] **Query Purpose**
  - Comment block at top explaining purpose
  - Input sources documented
  - Output documented

### Style Standards

- [ ] **SQL Syntax**
  - SQL keywords are CAPITALIZED
  - Explicit JOIN syntax used (INNER JOIN, LEFT JOIN, etc.)
  - No implicit joins
  - Table aliases used in multi-table queries
  - Aliases are meaningful (not just a, b, c)

- [ ] **Formatting**
  - Code has been formatted with Databricks integrated formatter
  - Each major SQL clause on its own line (SELECT, FROM, WHERE, GROUP BY, ORDER BY, LIMIT)
  - Column lists use 2-space indentation
  - Subqueries and JOINs properly indented
  - Consistent formatting throughout

- [ ] **Comment Placement**
  - Comments on their own lines before queries (not inline at end of WHERE clauses)
  - Comment blocks at top describing query purpose
  - No inline comments at end of statements

### Comments & Documentation

- [ ] **Query Comments**
  - Purpose comment at top of query
  - Major sections/steps commented
  - Complex business logic explained
  - Non-obvious filters documented

### Unity Catalog Tables & Views

- [ ] **Table Comments**
  - Table COMMENT describes purpose and contents
  - Data refresh frequency documented
  - Source data lineage documented (upstream tables/systems)
  - Owner/team documented

- [ ] **Column Comments**
  - Non-obvious columns have COMMENT
  - Business metrics have calculation explained
  - Specific formats or constraints documented
  - Foreign key relationships documented

- [ ] **Table Properties** (Optional but Recommended)
  - TBLPROPERTIES set for quality_tier, owner, pii_contains, retention_days
  - Partitioning documented in table comment

### Performance Considerations

- [ ] **Query Optimization**
  - Appropriate data types used (INT vs BIGINT, VARCHAR vs TEXT)
  - Consider partitioning for large tables
  - Query plan reviewed for expensive operations

### Security

- [ ] **SQL Injection Prevention**
  - No string concatenation for dynamic SQL
  - Parameterized queries used where needed
  - User inputs sanitized

---

## Documentation & Maintenance Review

### README Files

- [ ] **README Accuracy**
  - README reviewed after major changes
  - Stale sections removed
  - New features/changes documented
  - Setup instructions current
  - Dependencies up to date
  - Usage examples work

- [ ] **README Completeness**
  - Project purpose clearly stated
  - Prerequisites listed
  - Installation/setup steps provided
  - Usage examples included
  - Configuration explained

### In-Code Documentation

- [ ] **Documentation Updates**
  - Comments updated when code logic changed
  - TODO/FIXME comments added for known issues
  - Links to tickets/PRs included for context

### Notebook Documentation

- [ ] **Notebook Structure**
  - Markdown cells create narrative flow
  - Header cell optional (recommended for complex notebooks, may be overkill if sufficiently documented in README)
  - Data assumptions documented (freshness, schema, filters)
  - Sample outputs shown to verify transformations

- [ ] **Notebook Naming & Format**
  - Uses dashes instead of spaces (e.g., `customer-analysis` not `Customer Analysis`)
  - Source format preferred over Jupyter format for GitHub

- [ ] **Version Control**
  - All outputs cleared before committing
  - No temporary/test code left in notebook

---

## Version Control Review

### Commits

- [ ] **Commit Quality**
  - Clear, descriptive commit messages
  - Imperative mood ("Add feature" not "Added feature")
  - Commits focused on single logical change

### Code Hygiene

- [ ] **Cleanup**
  - No commented-out code blocks
  - No debug print statements
  - No temporary test code
  - .gitignore excludes generated files, credentials, local config

---

## Review Procedure

### Step 1: Automated Checks
Run these checks first to catch obvious issues:

```python
# For Python files:
# 1. Run linting (if available)
# 2. Run formatter
# 3. Run unit tests
```

```sql
-- For SQL files:
-- 1. Run formatter in Databricks
-- 2. Verify query executes without errors
```

### Step 2: Manual Code Review
Go through the appropriate checklist above (Python, SQL, or both) systematically.

### Step 3: Documentation Review
If working in a git repository:
1. Open the README file
2. Read through each section
3. Verify accuracy against current code
4. Remove stale information
5. Add documentation for new features

### Step 4: Final Verification
- [ ] Code runs without errors
- [ ] All tests pass
- [ ] Documentation is complete and accurate
- [ ] Security considerations addressed
- [ ] No secrets or credentials exposed

---

## Common Issues to Watch For

### Python Red Flags
* Bare `except:` without exception type
* Missing type hints on functions
* Missing or incomplete docstrings
* Functions with no clear single purpose
* Deep nesting (>4 levels)
* Magic numbers or hardcoded values
* Commented-out code left in
* Hardcoded credentials
* Inline comments at end of lines instead of before code

### SQL Red Flags
* Monolithic queries doing too many transformations
* Missing table/view comments in Unity Catalog
* Implicit joins (comma-separated tables in FROM)
* No comments explaining business logic
* Missing column comments on metrics
* String concatenation for dynamic SQL
* Clauses not on separate lines (e.g., `SELECT * FROM table WHERE condition`)
* Inline comments at end of WHERE/other clauses

### Documentation Red Flags
* README hasn't been updated in months
* Stale setup instructions
* Examples that don't work
* Undocumented new features
* Missing prerequisites or dependencies

---

## Quick Start Guide

**For a quick pre-submit review:**

1. **Python Code**: Check docstrings, type hints, naming conventions, no hardcoded secrets, comment placement
2. **SQL Code**: Check temp view organization, CAPITALIZE keywords, table COMMENT set, clause formatting
3. **Documentation**: If in git repo, review README for accuracy

**For a thorough review before merge:**
Work through the complete checklist for all relevant sections.

---

## Tips for Better Reviews

1. **Review in small chunks**: Don't try to review 1000+ lines at once
2. **Use the checklist**: Don't rely on memory, systematically go through items
3. **Look for patterns**: If you find one issue, check if it appears elsewhere
4. **Consider the reader**: Would someone new to the code understand it?
5. **Balance perfection and progress**: Not every item needs to be perfect, but standards should be met
6. **Automate what you can**: Use formatters and linters to catch style issues automatically

---

## Integration with Development Workflow

**Before starting peer review:**
1. Self-review using this checklist
2. Fix issues found
3. Run formatter and linters
4. Update documentation
5. Then request peer review

**During peer review:**
1. Reviewer uses this checklist
2. Provides specific feedback referencing checklist items
3. Discusses any disagreements about standards

**After review:**
1. Address feedback
2. Re-verify changed items on checklist
3. Confirm all items pass before merge
