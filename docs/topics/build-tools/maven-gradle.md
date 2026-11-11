# Maven and Gradle for Java Projects

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Short interview answer
Maven and Gradle automate dependency management, compilation, testing, packaging, and publishing. Maven uses a convention-driven XML POM and lifecycle phases. Gradle uses a task graph and a Groovy or Kotlin DSL, with incremental and configurable builds.

## 2. Maven lifecycle
Important default lifecycle phases include validate, compile, test, package, verify, install, and deploy. Running a later phase runs earlier phases in order.

```bash
mvn clean test
mvn clean package
mvn clean install
```

- package creates the JAR/WAR.
- install places the artifact in the local Maven repository.
- deploy publishes it to a remote repository.

Dependencies are declared in pom.xml. Dependency scopes include compile, provided, runtime, test, and system (avoid system scope in normal projects).

## 3. Gradle tasks and configuration
Gradle executes tasks according to their dependencies.

```bash
./gradlew clean test
./gradlew bootJar
./gradlew build
```

Use the Gradle Wrapper so the project controls the Gradle version. Dependencies are declared in build.gradle or build.gradle.kts. Common configurations include implementation, api, compileOnly, runtimeOnly, and testImplementation.

## 4. Dependency resolution
A transitive dependency is brought in by another dependency. Version conflicts may be resolved by dependency management rules, constraints, platforms/BOMs, or explicit version selection. Inspect the dependency tree instead of guessing:

```bash
mvn dependency:tree
./gradlew dependencies
./gradlew dependencyInsight --dependency jackson-databind
```

Spring Boot dependency management/BOM aligns compatible library versions. Avoid overriding managed versions without a reason and compatibility check.

## 5. Build reproducibility and CI
- Commit wrapper scripts and wrapper configuration.
- Pin plugin and tool versions.
- Avoid machine-specific absolute paths.
- Keep credentials out of build files.
- Cache dependencies in CI where safe.
- Run tests and package the same source revision.
- Produce versioned artifacts and retain build metadata.

## 6. Maven vs Gradle

| Maven | Gradle |
|---|---|
| XML POM | Groovy/Kotlin DSL |
| Fixed lifecycle phases | Flexible task graph |
| Convention-driven | Highly configurable |
| Mature and widely used | Often faster for large incremental builds when configured well |

Neither is universally better. Follow the project ecosystem and team needs.

## 7. Common mistakes
- Confusing install with deploy.
- Editing generated wrapper files manually without understanding them.
- Adding a dependency without checking transitive versions.
- Using dynamic versions for production builds.
- Skipping tests to make CI green.
- Assuming local IDE build proves the CI build is reproducible.

## 8. Interview questions
**Q1. package vs install in Maven?** package creates the artifact; install also places it in the local repository.

**Q2. What is a BOM?** A dependency-management mechanism that aligns versions of related libraries.

**Q3. Why use Gradle Wrapper?** It makes builds use the project's declared Gradle version.

**Q4. What is a transitive dependency?** A dependency introduced through another dependency.

**Q5. How do you debug a version conflict?** Inspect the dependency tree/insight report and identify which path brings each version.

## 9. Practice
1. Add a test-scoped dependency and explain why it is not in production runtime.
2. Inspect the dependency tree for a Spring Boot starter.
3. Build the same project locally and in CI using the wrapper.
