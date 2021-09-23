# aoc-java-user
Why does `./gradlew run` work, while `./gradlew test` fails with:

```text
FAILURE: Build failed with an exception.
3 actionable tasks: 2 executed, 1 up-to-date

* What went wrong:
Execution failed for task ':test'.
> Process 'Gradle Test Executor 1' finished with non-zero exit value 132
  This problem might be caused by incorrect test process configuration.
  Please refer to the test execution section in the User Manual at https://docs.gradle.org/7.2/userguide/java_testing.html#sec:test_execution
```

The main method in [Main.java](src/main/java/Main.java) and the test in [CrashTest.java](src/test/java/CrashTest.java) are very similar:

```java
    // Main method that works:
    public static void main(String[] args) {
        var answer = Solver.solve(2019, 1, 1, "14");
        System.out.println(answer);
    }

    // Test method that crashes:
    @Test
    void whyDoesThisCrash() {
        var answer = Solver.solve(2019, 1, 1, "14");
        System.out.println(answer);
    }
```

It most certainly is caused by usage of JNI, with the library loaded by [JarNativeLibraryLoader.java](https://github.com/fornwall/advent-of-code/blob/master/crates/java/java-src/src/main/java/net/fornwall/aoc/JarNativeLibraryLoader.java)
and the JNI code accessed using from [Solver.java](https://github.com/fornwall/advent-of-code/blob/master/crates/java/java-src/src/main/java/net/fornwall/aoc/Solver.java), with the native code
in [lib.rs](https://github.com/fornwall/advent-of-code/blob/master/crates/java/src/lib.rs) - but what causes the crash, and why only when testing?
