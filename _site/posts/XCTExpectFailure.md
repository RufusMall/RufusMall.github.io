Hi everyone,

In this blog post, I want to share with you a handy feature of XCTest framework that can help you write better tests: XCTExpectFailure.

XCTExpectFailure is a function that allows you to mark a test assertion as expected to fail. This means that if the assertion fails, the test will not fail, but if the assertion passes, the test will fail. This is useful for cases where you have a known bug or limitation in your code that you want to document with a test, but you don't want to make your test suite red until you fix it.

Let me show you an example of how to use XCTExpectFailure. Suppose you have a function that calculates the factorial of a given integer:

```
func factorial(_ n: Int) -> Int {
  guard n >= 0 else { return -1 }
  if n == 0 || n == 1 {
    return 1
  } else {
    return n * factorial(n - 1)
  }
}
```

Now, you want to write some tests for this function, but you know that it has a bug: it will overflow for large values of n. You can use XCTExpectFailure to mark this test as expected to fail:

```
func testFactorialOverflow() {
  // This assertion will fail because factorial(21) is larger than Int.max
  XCTExpectFailure("Factorial function overflows for large values of n") {
    XCTAssertEqual(factorial(21), 51090942171709440000)
  }
}
```
When you run this test, you will see something like this in the console:
```
Test Case '-[FactorialTests testFactorialOverflow]' started.
XCTExpectFailure: Factorial function overflows for large values of n - Assertion failed but was expected to fail.
Test Case '-[FactorialTests testFactorialOverflow]' passed (0.001 seconds).
```
As you can see, the test passed even though the assertion failed, because it was marked with XCTExpectFailure. However, if you fix the bug in your factorial function and make it handle large values of n correctly, the test will fail:

```
Test Case '-[FactorialTests testFactorialOverflow]' started.
XCTExpectFailure: Factorial function overflows for large values of n - Assertion passed unexpectedly.
/Users/.../FactorialTests.swift:18: error: -[FactorialTests testFactorialOverflow] : XCTAssertEqual failed: ("51090942171709440000") is not equal to ("51090942171709440000") - 
Test Case '-[FactorialTests testFactorialOverflow]' failed (0.001 seconds).
```

This way, XCTExpectFailure helps you keep track of the known issues in your code and reminds you to fix them when they are no longer valid.

Personally I don't think the above scenario is too useful in practice and might actually cause issues. However, one area where I have found a good practical use is to ensure a third party test framework is working correctly.

If you are using a third party test framework that internally calls XCTAssert functions on your behalf, you can add some tests to your consuming code to ensure the assertions are working as expected. These tests will then alert you if an upgrade or switch to an alternative third party framework no longer asserts for the the cases you expect it to.

```
func testSnapshotFrameworkFailsIfImagesDontMatch() {
  // This assertion should fail as the snapshots do not match
  XCTExpectFailure("Should assert if images don't match") {
    // invoke third party snapshot testing framework here.
  }
}
```

I hope you found this blog post useful and learned something new about XCTest framework. Thanks for reading!