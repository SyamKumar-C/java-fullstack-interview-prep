# Docker, CI/CD and Deployment Strategies

> Priority: P1 | Level: Intermediate | Study time: 50 minutes

## 1. Short interview answer
Docker packages an application and runtime dependencies into an image. A container is a running instance of an image. CI builds and tests changes; CD delivers or deploys verified artifacts. A safe pipeline uses repeatable builds, external configuration, health checks, and rollback.

## 2. Image vs container
- Image: immutable template made of layers.
- Container: isolated running process created from an image.
- Registry: stores and distributes images.
- Volume: persistent data outside the container writable layer.
- Port mapping: exposes a container port through the host/runtime.

A container is not a full virtual machine; it shares the host kernel with isolation.

## 3. Spring Boot Dockerfile

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY build/libs/app.jar app.jar
USER 10001
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Use a trusted, pinned base image and non-root user. Do not bake secrets into image layers. Use .dockerignore to keep local secrets and unnecessary files out of the build context.

## 4. CI/CD pipeline
1. Checkout source.
2. Compile and run unit tests.
3. Run static analysis and dependency/security checks.
4. Build a versioned artifact/image.
5. Push to registry.
6. Deploy to test/staging.
7. Run smoke/integration checks.
8. Promote the same artifact to production.
9. Verify health and monitor errors.
10. Roll back or roll forward if checks fail.

Build once and promote the same artifact; avoid separate rebuilds per environment.

## 5. Deployment strategies

| Strategy | Idea | Trade-off |
|---|---|---|
| Rolling | Replace instances gradually | Mixed versions temporarily |
| Blue-green | Switch traffic between two environments | Extra infrastructure during switch |
| Canary | Send small traffic share to new version | Needs metrics and traffic control |
| Recreate | Stop old, start new | Downtime likely |

Database migrations must support old and new app versions during rolling deploys. Expand-and-contract: add compatible schema, deploy code supporting both, migrate data, then remove old schema in a later release.

## 6. Configuration and secrets
Keep environment configuration outside the image. Use environment variables, managed configuration, or secret stores. Rotate secrets, grant least privilege, and never log credentials.

## 7. Common mistakes
- Using latest as the only production version.
- Running as root unnecessarily.
- Storing secrets in Dockerfile or source control.
- Rebuilding different artifacts for staging and production.
- Deploying a breaking schema change before compatible app versions.
- Having no health check or rollback path.

## 8. Interview questions
**Q1. Image vs container?** Image is the packaged template; container is a running instance.

**Q2. Why multi-stage builds?** Keep build tools out of the runtime image and reduce size/attack surface.

**Q3. CI vs CD?** CI validates changes continuously; CD automates delivery/deployment of validated artifacts.

**Q4. Rolling vs blue-green?** Rolling replaces instances gradually; blue-green switches traffic between environments.

**Q5. Why expand-and-contract?** It allows old and new application versions to coexist during deployment.

## 9. Practice
1. Containerize a Spring Boot JAR.
2. Design a pipeline that tests and publishes an image.
3. Rename a database column without downtime.
4. Choose a deployment strategy for a high-traffic API.
