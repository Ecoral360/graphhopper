# GraphHopper Testing Assignment - Tache 2

**Author**: Karim Hozaien  
**Date**: November 17, 2025

## Overview

This assignment demonstrates two testing concepts for the GraphHopper project:
1. Unit testing with Mockito
2. Humorous test failure handling with rickroll

## Part 1: Mockito Unit Tests

### Files Created

- `core/pom.xml` - Added Mockito dependencies (5.7.0)
- `core/src/test/java/com/graphhopper/routing/FlexiblePathCalculatorMockTest.java`
- `core/src/test/java/com/graphhopper/routing/RouterMockTest.java`

### Classes Tested

#### FlexiblePathCalculator (8 tests)

**Justification**: Orchestrates routing algorithms with multiple mockable dependencies.

**Mocked classes**:
- QueryGraph - Query graph representation
- RoutingAlgorithmFactory - Creates routing algorithms
- RoutingAlgorithm - Executes path calculations
- Weighting - Calculates route costs

**Test coverage**:
- Successful path calculation
- Edge restrictions (curbsides)
- Unfavored edges handling
- Empty path exception
- Maximum nodes exceeded exception
- Dynamic weighting changes
- Incompatible algorithm detection
- Multiple alternative paths

#### Router (13 tests)

**Justification**: Main entry point with complex validation logic and multiple dependencies.

**Mocked classes**:
- BaseGraph - Main graph structure
- EncodingManager - Handles route property encodings
- LocationIndex - Spatial index for coordinates
- WeightingFactory - Creates weighting strategies

**Test coverage**:
- No points validation
- Legacy parameter rejection (vehicle, weighting, turn_costs, block_area)
- Out of bounds points
- Incorrect heading/hint/curbside counts
- Null point handling
- Invalid coordinates
- Missing subnetwork configuration
- Internal custom model rejection

### Mock Justification

Mocks were chosen to:
- Avoid loading OSM data (minutes of setup)
- Test logic independently of implementation
- Simulate edge cases (errors, limits)
- Ensure fast, deterministic tests

### Test Values

All values chosen for realism:
- Coordinates: Paris locations (48.8566, 2.3522)
- Distances: 100-200 meters
- Node counts: 50-200 visited nodes
- Edge IDs: Representative values (1-10)

### Statistics

- Total tests: 21
- Classes tested: 2
- Classes mocked: 8 (4 per test class)
- Execution time: Less than 1 second

## Part 2: Rickroll on Test Failure

### Files Created

- `.github/actions/rickroll-on-failure/action.yml` - Custom composite action
- `.github/actions/rickroll-on-failure/README.md` - Action documentation
- `.github/workflows/rickroll-tests.yml` - Workflow implementation

### Implementation

**Approach**: Custom GitHub Actions composite action

**Advantages**:
- Fully customizable
- Reusable across workflows
- No external dependencies
- Simple bash implementation
- Fast execution

**Alternatives considered**:
- Existing rickroll actions (less flexible)
- Rust implementation (overcomplicated)
- Maven plugin (limited to Maven, not visible in CI)

### How It Works

1. Tests run with `continue-on-error: true`
2. Test outcome captured in `steps.test.outcome`
3. Action checks if outcome is 'failure'
4. Displays rickroll message if failed
5. Workflow fails after rickroll displayed

### Workflow Configuration

```yaml
- name: Run Tests
  id: test
  run: mvn test
  continue-on-error: true

- name: Rickroll on Failure
  if: always()
  uses: ./.github/actions/rickroll-on-failure
  with:
    test-result: ${{ steps.test.outcome }}
```

### Output on Failure

```
========================================
NEVER GONNA GIVE YOU UP
========================================

Test execution failed.

Reference: https://www.youtube.com/watch?v=dQw4w9WgXcQ

Branch: karim
Commit: ce4ff049c...
Author: karimhozaien

========================================
```

### Technical Details

- Runs only on branch `karim`
- Targets specific tests: FlexiblePathCalculatorMockTest, RouterMockTest
- Creates GitHub Actions annotation
- Preserves workflow failure status

## Repository Structure

```
graphhopper-1/
├── .github/
│   ├── actions/rickroll-on-failure/
│   │   ├── action.yml
│   │   └── README.md
│   └── workflows/
│       └── rickroll-tests.yml
├── core/
│   ├── pom.xml (modified)
│   └── src/test/java/com/graphhopper/routing/
│       ├── FlexiblePathCalculatorMockTest.java
│       └── RouterMockTest.java
└── tache2-remise/
    ├── README.md (this file)
    ├── mockito-tests-documentation.md
    └── rickroll-documentation.md
```

## Git History

```
Branch: karim
Commits:
- 7e61fa45c: Ajout de tests unitaires avec Mockito
- ce4ff049c: Ajout du rickroll sur échec des tests
```

## Execution

### Running Tests Locally

```bash
mvn test -pl core -Dtest=FlexiblePathCalculatorMockTest,RouterMockTest
```

### Running with Rickroll (CI)

Push to branch `karim`:
```bash
git push origin karim
```

The workflow `rickroll-tests.yml` executes automatically and displays the rickroll if tests fail.

## Key Achievements

1. Comprehensive Mockito testing demonstrating isolation principles
2. Realistic test values based on actual use cases
3. Custom GitHub Action implementation
4. Complete documentation and justification
5. Working CI/CD integration
6. Professional code quality

## Dependencies Added

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.7.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.7.0</version>
    <scope>test</scope>
</dependency>
```

## Notes

- Project has pre-existing compilation errors in main source
- Tests are isolated and compile independently
- Mockito version 5.7.0 chosen for Java 17 compatibility
- Rickroll action is reusable in other projects

## References

- GraphHopper: https://github.com/graphhopper/graphhopper
- Mockito Documentation: https://javadoc.io/doc/org.mockito/mockito-core
- GitHub Actions: https://docs.github.com/en/actions
- The Original: https://www.youtube.com/watch?v=dQw4w9WgXcQ

