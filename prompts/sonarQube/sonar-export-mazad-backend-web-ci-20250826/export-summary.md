# SonarQube Code Smells Export Summary

**Project:** Mazad_Backend-Web-CI  
**Scope:** overall  
**Export Date:** 2025-08-26  
**Export Time:** Complete  

## Export Statistics

| Severity | Count | Files |
|----------|-------|-------|
| BLOCKER  | 0     | -     |
| CRITICAL | 0     | -     |
| MAJOR    | 280   | Files 1-6 |
| MINOR    | 22    | File 6   |
| INFO     | 0     | -     |
| **TOTAL** | **302** | **6 files** |

## File Breakdown

1. **sonar-issues-001.json** - MAJOR_001 to MAJOR_050 (50 issues)
2. **sonar-issues-002.json** - MAJOR_051 to MAJOR_100 (50 issues)  
3. **sonar-issues-003.json** - MAJOR_101 to MAJOR_150 (50 issues)
4. **sonar-issues-004.json** - MAJOR_151 to MAJOR_200 (50 issues)
5. **sonar-issues-005.json** - MAJOR_201 to MAJOR_250 (50 issues)
6. **sonar-issues-006.json** - MAJOR_251 to MAJOR_280 + MINOR_001 to MINOR_022 (52 issues)

## Issue Categories

### MAJOR Issues (280 total)
- **Async/Await Violations:** ~150 issues (csharpsquid:S6966)
  - "Await ToListAsync instead"
  - "Await CountAsync instead"  
  - "Await GetCurrentUserAsync instead"
  - "Await FirstOrDefaultAsync instead"

- **Format Provider Issues:** ~40 issues (csharpsquid:S6580)
  - "Use a format provider when parsing date and time"

- **Unused Parameters:** ~70 issues (csharpsquid:S1172)
  - "Remove the unused parameter 'cancellationToken'"
  - Primarily in event handlers

- **Unused Fields:** ~5 issues (csharpsquid:S1144)
  - Private fields never used

- **Code Logic Issues:** ~10 issues
  - Unnecessary null checks (csharpsquid:S2589)
  - Always true conditions
  - Attribute usage specifications (csharpsquid:S3993)

- **Naming Convention:** ~5 issues (csharpsquid:S101)
  - Parameter naming issues (FinanicalTransactionEnum vs FinancialTransactionEnum)

### MINOR Issues (22 total)
- **Naming Conventions:** ~20 issues (csharpsquid:S2339)
  - Class names not in PascalCase
  - Method/member names with underscores
  - Class naming pattern violations

- **Design Issues:** ~2 issues
  - Missing async/await usage (csharpsquid:S4456)
  - String literals should be constants (csharpsquid:S1848)

## Key Findings

1. **High Async/Await Violations**: 150+ issues indicate systematic problems with Entity Framework async patterns
2. **Event Handler Pattern**: 70+ unused cancellationToken parameters suggest consistent pattern across event handlers  
3. **Date Parsing Issues**: 40+ format provider issues indicate potential globalization problems
4. **Naming Standards**: Multiple naming convention violations, particularly with underscores and casing

## Data Collection Method

Used **Complete Dataset Collection Strategy** due to SonarQube API pagination failures:
- Tested pagination (skip parameter returned identical results)
- Collected complete datasets by severity with high limits (300 per call)
- Manually paginated into exactly 50-issue files
- Applied sequential ID assignment (MAJOR_001, MAJOR_002, etc.)

## File Schema

Each JSON file contains issues with standardized schema:
```json
{
  "id": "MAJOR_001",
  "severity": "MAJOR", 
  "type": "CODE_SMELL",
  "status": "OPEN",
  "message": "Issue description",
  "rule": "csharpsquid:SXXXX",
  "file": "src/path/to/file.cs",
  "line": 123,
  "assignee": "username|Unassigned",
  "created": "YYYY-MM-DD",
  "tags": ["tag1", "tag2"],
  "effort": "Xmin"
}
```

## Export Validation

✅ Total issues: 302 (matches SonarQube query)  
✅ Sequential ID assignment: MAJOR_001 through MAJOR_280, MINOR_001 through MINOR_022  
✅ File organization: Exactly 50 issues per file (except final file with 52)  
✅ Schema compliance: All required fields present  
✅ Data integrity: Real SonarQube issue data with proper metadata  

**Export Status: COMPLETE**
