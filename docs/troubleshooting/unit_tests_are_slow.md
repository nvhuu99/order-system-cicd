Refs:
https://docs.junit.org/current/user-guide/#writing-tests-parallel-execution
https://rieckpil.de/reuse-containers-with-testcontainers-for-fast-integration-tests/


## Problems & solutions:

Config file `junit-platform.properties`:

    junit.jupiter.execution.parallel.enabled = true
    junit.jupiter.execution.parallel.mode.default = concurrent
    junit.jupiter.execution.parallel.config.dynamic.factor = 4

Sample test arguments:

    mvnw test -DwithRedis=yes -DwithKafka=no

**1. ApplicationContext re-boot on every test classes:** need to create a base class with annotation @SpringBootTest, all test-classes will extend it. This way, ApplicationContext will boot only once for all test-classes.

**2. Need to run test in parallel:** use the config above for JUnit.

**3. Testcontainers need to create once for all test-classes:** In the base class, create containers in the static block. Also, to avoid create containers that are not used, pass and check conditions with test arguments.

**4. Testcontainers images pulling take very long on Jenkins agent**: Use PVC + Pre-pull (see ./testcontainers_pull_fail.md)

## Result:

    Environment: Local Intellij
    Resources: maximum 8 CPU cores
    Threads per core: 4
    Before: ~3min30s
    After:  ~35s

    Environment: Jenkins Agent (kind-kube-pod)
    Resources: maximum 2 CPU cores
    Threads per core: 4
    Before: ~7min30s
    After:  ~1min45s
