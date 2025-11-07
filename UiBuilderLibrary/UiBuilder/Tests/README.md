# UiBuilder Library Test Suite

This directory contains comprehensive unit tests for the UiBuilder library using the TcUnit testing framework.

## Test Coverage

The test suite covers the following components:

### 1. ST_HMICommand Tests (`FB_ST_HMICommand_Test`)
Tests for the HMI command data structure:
- Initial state verification
- Individual command flags (Enable, Disable, Home, Reset, Stop, Trigger)
- Error code handling (BYTE values 0-255)
- Multiple simultaneous commands
- Command clearing

### 2. ST_HMIComponentAPI Tests (`FB_ST_HMIComponentAPI_Test`)
Tests for the component API status structure:
- Initial state verification
- Individual status flags (Done, Busy, Error, ErrorID, Ready, Enabled, Homed, Moving)
- State machine transitions (Ready → Busy → Done)
- Error state handling
- Busy/Done mutual exclusivity
- All flags set/clear operations

### 3. ST_HMIStatus Tests (`FB_ST_HMIStatus_Test`)
Tests for the HMI status wrapper structure:
- Initial state verification
- HmiStatus member access
- Status flag operations
- State machine transitions through HmiStatus

### 4. FB_HMIBase Tests (`FB_HMIBase_Test`)
Tests for the base HMI function block:
- Function block instantiation
- Property access patterns (P_Command, P_Configuration, P_Status)
- Property read/write operations
- Data integrity verification

**Note:** Some FB_HMIBase tests contain placeholder code that will be activated once property implementations are complete. These are marked with comments in the test code.

## Running the Tests

### Prerequisites
- TwinCAT 3 (version 3.1.4026.18 or later)
- TcUnit testing framework v1.3.1 (already included in project)
- UiBuilder library project loaded in TwinCAT

### Steps to Run Tests

1. **Open the Project**
   - Open `UiBuilderLibrary.sln` in Visual Studio with TwinCAT XAE
   - Ensure all test files are loaded (check Solution Explorer)

2. **Configure Test Runner**
   - Option A: Add `PRG_TEST` to your PlcTask
   - Option B: Call `PRG_TEST()` from your `MAIN` program

3. **Build the Project**
   - Build → Build Solution (or F7)
   - Resolve any compilation errors

4. **Download to PLC**
   - Activate Configuration
   - Login to PLC
   - Start the PLC

5. **View Test Results**
   - Open TwinCAT Error List window
   - Check for TcUnit test results
   - Green = Pass, Red = Fail
   - Review detailed assertion messages for any failures

### Alternative: Running Tests from MAIN

Add this to your `MAIN.TcPOU`:

```
PROGRAM MAIN
VAR
    testRunner : PRG_TEST;
END_VAR

// Run tests
testRunner();
```

## Test Structure

Each test suite follows this structure:

```
FUNCTION_BLOCK FB_<Component>_Test EXTENDS TcUnit.FB_TestSuite
VAR
    // Test variables
END_VAR

// Main implementation calls all test methods
Test_Method1();
Test_Method2();
...

// Individual test methods
METHOD Test_Method1
    TEST('Test_Method1');
    // Test logic with assertions
    AssertTrue(...);
    AssertEquals(...);
    TEST_FINISHED();
END_METHOD
```

## TcUnit Assertions Used

- `AssertTrue(condition, message)` - Verifies condition is TRUE
- `AssertFalse(condition, message)` - Verifies condition is FALSE
- `AssertEquals_BYTE(expected, actual, message)` - Verifies byte values match
- More assertions available in TcUnit documentation

## Test Metrics

| Component | Test Methods | Assertions | Coverage |
|-----------|-------------|------------|----------|
| ST_HMICommand | 10 | ~30 | 100% |
| ST_HMIComponentAPI | 13 | ~40 | 100% |
| ST_HMIStatus | 4 | ~15 | 100% |
| FB_HMIBase | 7 | ~20 | Partial* |

*FB_HMIBase tests are ready but some require property implementation to be complete.

## Adding New Tests

To add tests for new components:

1. **Create Test Function Block**
   ```
   FUNCTION_BLOCK FB_YourComponent_Test EXTENDS TcUnit.FB_TestSuite
   ```

2. **Add Test Methods**
   ```
   METHOD Test_YourFeature
       TEST('Test_YourFeature');
       // Test code
       AssertTrue(condition, 'Message');
       TEST_FINISHED();
   END_METHOD
   ```

3. **Update PRG_TEST**
   ```
   VAR
       fbYourTest : FB_YourComponent_Test;
   END_VAR

   fbYourTest();
   ```

4. **Update Project File**
   - Add test file to `<ItemGroup>` with `<Compile Include="Tests\FB_YourComponent_Test.TcPOU">`

## Troubleshooting

### Tests Not Running
- Verify PRG_TEST is called in your task or MAIN
- Check TcUnit library is referenced correctly
- Ensure PLC is in RUN mode

### Compilation Errors
- Check all test files are included in project
- Verify TcUnit namespace is accessible
- Ensure data types are defined before tests

### Test Failures
- Review assertion messages in Error List
- Check test logic against component implementation
- Verify test data setup is correct

## Best Practices

1. **Test Naming**: Use descriptive test method names (Test_FeatureName)
2. **Assertions**: Include clear, descriptive messages
3. **Independence**: Each test should be independent (no shared state)
4. **Coverage**: Test normal cases, edge cases, and error conditions
5. **Documentation**: Comment complex test logic

## References

- [TcUnit Documentation](https://tcunit.org)
- [TwinCAT 3 Documentation](https://infosys.beckhoff.com)
- UiBuilder Library Design Specifications (if available)

## Version History

- v1.0.0 (2025-10-31): Initial test suite creation
  - Complete test coverage for ST_HMICommand
  - Complete test coverage for ST_HMIComponentAPI
  - Complete test coverage for ST_HMIStatus
  - Partial test coverage for FB_HMIBase (awaiting property implementation)
