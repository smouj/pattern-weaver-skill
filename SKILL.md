---
name: Pattern Weaver
version: 2.1.0
description: Discovers and connects hidden patterns across codebases and documentation
author: SMOUJBOT
tags:
  - patterns
  - code-analysis
  - documentation
  - discovery
  - connections
  - refactoring
  - architecture
type: analysis
dependencies:
  - git
  - ripgrep (rg)
  - jq
  - python3
  - python3-pip
requirements:
  - python_modules:
    - networkx
    - matplotlib
    - scikit-learn
    - nltk
  - minimum_python: "3.9"
  - disk_space_mb: 500
environment_variables:
  - PATTERN_WEAVER_CACHE_DIR: "~/.cache/pattern-weaver"
  - PATTERN_WEAVER_MAX_FILES: "10000"
  - PATTERN_WEAVER_MIN_CONFIDENCE: "0.7"
  - PATTERN_WEAVER_IGNORE_PATTERNS: ".git,node_modules,build,dist"
---

# Pattern Weaver Skill

## Purpose

Pattern Weaver analyzes codebases to discover hidden structural, architectural, and behavioral patterns. It identifies repeated code structures, architectural anti-patterns, design pattern implementations, and cross-module dependencies. The skill connects related patterns across files, tracks pattern evolution over Git history, and generates interactive visualizations showing code relationships.

Real use cases:
- Finding copy-pasted code blocks across 50+ files for refactoring
- Discovering undocumented design patterns in legacy code
- Identifying circular dependencies causing build failures
- Mapping module coupling before major refactoring
- Tracing pattern drift between documentation and implementation
- Finding architectural violations in microservices boundaries
</think>
```markdown
---

## Scope

### Primary Commands

**`pattern-weaver scan [PATH]`**
Scans codebase for patterns. Options:
- `--extensions ts,js,py,java` - limit file types
- `--min-lines 5` - minimum lines for pattern detection
- `--mode structural|behavioral|architectural`
- `--output json|graphviz|html`
- `--threshold 0.8` - similarity confidence threshold (0-1)
- `--exclude-dir test,tests,spec`
- `--history 30` - analyze Git history (days)

**`pattern-weaver connect PATTERN_ID`**
Finds related patterns across codebase. Options:
- `--depth 3` - connection depth
- `--include-cross-file` - connect patterns across files
- `--min-strength 0.6` - minimum connection strength
- `--format tree|graph|list`

**`pattern-weaver visualize PATTERN_ID`**
Generates interactive graph visualization. Options:
- `--type dependency|coupling|evolution`
- `--output interactive|static`
- `--format html|png|svg`
- `--cluster` - auto-cluster related nodes
- `--highlight PATTERN_ID` - emphasize specific pattern

**`pattern-weaver track PATTERN_ID`**
Shows pattern evolution over time. Options:
- `--git-commits` - track through Git history
- `--by-author` - group changes by author
- `--change-types addition|modification|deletion`
- `--since "2024-01-01"` - time range

**`pattern-weaver report`**
Generates comprehensive analysis report. Options:
- `--format markdown|json|sarif`
- `--include heatmap,cluster,drift`
- `--output-dir ./reports`
- `--compare-branch feature/new-arch`

**`pattern-weaver suggest-refactor PATTERN_ID`**
Provides specific refactoring recommendations. Options:
- `--strategy extract-method|pull-up|split-class`
- `--impact low|medium|high`
- `--include migration-steps`
- `--dry-run` - show suggestions without modifications

**`pattern-weaver validate`**
Validates discovered patterns against quality gates. Options:
- `--gate complexity,duplication,coupling`
- `--fail-on violation` - exit with non-zero on violations
- `--thresholds complexity=15,duplication=0.3`

---

## Work Process

1. **Discovery Phase**
   - Run `pattern-weaver scan` with structural mode
   - System indexes all source files, builds ASTs
   - Detects: repeated blocks, similar functions, identical error handling
   - Stores normalized representations in cache

2. **Analysis Phase**
   - Run `pattern-weaver scan` with architectural mode
   - Analyzes import graphs, call graphs, module dependencies
   - Identifies cycles, hub classes, god modules
   - Cross-references with behavioral patterns

3. **Connection Phase**
   - For each pattern, run `pattern-weaver connect`
   - Builds pattern relationship graph
   - Calculates connection strength via:
     - Structural similarity (AST distance)
     - Call frequency (execution trace analysis)
     - Co-change history (Git commit pairs)
     - Documentation overlap (term frequency)

4. **Visualization Phase**
   - Generate interactive HTML graph with `visualize --output interactive`
   - Color-code: red (violations), blue (patterns), green (documented)
   - Layout: force-directed, hierarchically clustered
   - Enable filtering by pattern type, confidence, file

5. **Validation Phase**
   - Run `pattern-weaver validate` against quality gates
   - Flag patterns exceeding thresholds:
     - Cyclomatic complexity > 15
     - Code duplication > 30%
     - Efferent coupling > 20
     - Inconsistent naming across occurrences

6. **Recommendation Phase**
   - Generate refactor suggestions for grouped patterns
   - Prioritize by: frequency, risk, maintenance cost
   - Provide migration paths preserving behavior
   - Estimate effort and impact metrics

---

## Golden Rules

1. **Cache Invalidation**: Always run with `--force-refresh` after major refactors
2. **Threshold Adjustment**: Use `--min-confidence 0.9` for production code, `0.6` for exploratory analysis
3. **Exclude Generated Code**: Always add `--exclude-dir` for auto-generated files to avoid noise
4. **Git Baseline**: Use `--history 90` on mature codebases for accurate evolution tracking
5. **Memory Management**: Set `PATTERN_WEAVER_MAX_FILES` to prevent OOM on huge codebases
6. **Pattern Grouping**: Manually review auto-clustered patterns before refactoring - algorithm may group unrelated similarities
7. **Security**: Never feed proprietary code to Pattern Weaver running on untrusted infrastructure
8. **Incremental Analysis**: For large repos, run per-directory and combine results with `pattern-weaver merge-results`
9. **Documentation Sync**: Always cross-reference with `docs/` directory using `--include-docs` flag
10. **False Positive Filter**: Use `pattern-weaver ignore PATTERN_ID` to blacklist noise patterns from future runs

---

## Examples

### Example 1: Find duplicated error handling
```
pattern-weaver scan src/ --extensions ts,js --mode structural --min-lines 8 --threshold 0.85
```
Output: `Found 23 similar code blocks (avg 12 lines, 87% similarity)`

### Example 2: Connect related singleton implementations
```
pattern-weaver connect singleton-pattern --depth 2 --include-cross-file --format graph
```
Output: Graph showing 5 singleton implementations, 2 violate thread-safety

### Example 3: Visualize module coupling before refactoring
```
pattern-weaver visualize coupling --type dependency --output interactive --cluster
```
Output: `interactive-graph.html` with expandable clusters, hover shows metrics

### Example 4: Track factory pattern evolution
```
pattern-weaver track factory-method --git-commits --by-author --since "2024-06-01"
```
Output: Timeline showing 4 major changes, 3 different authors, increasing complexity

### Example 5: Generate refactoring report
```
pattern-weaver report --format sarif --include heatmap,cluster,drift --output-dir ./reports/ --compare-branch feature/clean-arch
```
Output: `reports/analysis-20250303.sarif` ready for CI/CD integration

### Example 6: Validate architecture against complexity rules
```
pattern-weaver validate --gate complexity,duplication,coupling --fail-on violation --thresholds complexity=12,duplication=0.25
```
Output: Exit code 1 if violations found, stdout lists specific files/lines

---

## Verification

After running Pattern Weaver:

1. Check cache directory exists: `ls $PATTERN_WEAVER_CACHE_DIR`
2. Verify graph generation: `test -f pattern-graph.html`
3. Validate AST integrity: Randomly inspect 3 patterns with `pattern-weaver show PATTERN_ID`
4. Ensure no false positives: Manually review patterns with confidence > 0.95
5. Confirm memory usage stays under 80% of system RAM for repos < 10k files

---

## Rollback

If Pattern Weaver introduces issues:

1. **Clear cache**: `rm -rf $PATTERN_WEAVER_CACHE_DIR`
2. **Disable integration**: Remove Pattern Weaver hooks from CI/CD pipeline
3. **Revert automated refactors**: Use git to revert commits created by `suggest-refactor`
   - Find commits: `git log --grep="Pattern Weaver refactor"`
   - Revert: `git revert --no-edit <commit-hash>`
4. **Restore previous reports**: Copy from backup location if report overwrites occurred
5. **Re-run with conservative thresholds**: `pattern-weaver scan --threshold 0.95 --exclude-dir pattern-weaver-backup`
6. **Check for side effects**: Run test suite to ensure no behavioral changes from analysis

---

## Troubleshooting

**Issue**: Pattern Weaver hangs on large repository
**Solution**: Set `PATTERN_WEAVER_MAX_FILES=5000` and run per-module: `pattern-weaver scan src/module1/`, then merge results

**Issue**: High memory usage causing OOM
**Solution**: Increase swap, reduce `--min-lines` threshold, or exclude large generated directories

**Issue**: Too many false positives (spurious pattern matches)
**Solution**: Increase `--threshold` to 0.9, add `--exclude-pattern "test.*,mock.*"` for test files, run with `--strict-ast` flag

**Issue**: Graph visualization fails with "node count exceeds limit"
**Solution**: Use `--cluster` to reduce node count, or filter: `pattern-weaver visualize --min-strength 0.8`

**Issue**: Git history analysis slow
**Solution**: Limit range: `--since "1 year ago"` or use `--git-commits 100` to sample last 100 commits

**Issue**: Missing pattern connections
**Solution**: Increase `--depth` to 4 or 5, ensure `--include-cross-file` is set, check that files aren't excluded by `PATTERN_WEAVER_IGNORE_PATTERNS`

**Issue**: Python import errors (networkx, matplotlib)
**Solution**: Install dependencies: `pip3 install networkx matplotlib scikit-learn nltk` and run `python3 -c "import nltk; nltk.download('punkt')"`

**Issue**: AST parsing fails on specific files
**Solution**: Exclude problematic extensions: `--exclude-ext min.js,bundle.js` or upgrade tree-sitter parsers

---

## Advanced Usage

**Batch Processing Multiple Repos**:
```bash
for repo in repos/*; do
  pattern-weaver scan "$repo" --output json --threshold 0.8 > "reports/$(basename $repo)-patterns.json"
done
pattern-weaver merge-reports reports/*.json --output consolidated-report.html
```

**CI/CD Integration**:
```yaml
- name: Pattern Analysis
  run: |
    pattern-weaver scan . --mode architectural --threshold 0.85 --output sarif > patterns.sarif
    pattern-weaver validate --fail-on violation
    pattern-weaver report --format markdown > PATTERNS.md
```

**Custom Pattern Definition**:
Create `~/.config/pattern-weaver/custom-patterns.yaml`:
```yaml
patterns:
  - id: custom-retry-logic
    signature: "for.*attempt.*max_retries"
    tags: ["resilience", "retry"]
    recommended_refactor: "extract-retry-handler"
```

Run with: `pattern-weaver scan --custom-patterns ~/.config/pattern-weaver/custom-patterns.yaml`

---
```