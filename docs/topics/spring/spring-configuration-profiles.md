# Spring Boot Configuration and Profiles

## 1. Externalized configuration

Applications need environment-specific values such as database URLs, credentials, feature flags, and service endpoints. Spring Boot supports external configuration so code does not need to change between local, test, staging, and production environments.

Common sources include properties/YAML files, environment variables, command-line arguments, and external configuration systems. Property-source precedence matters: higher-priority sources can override lower-priority values.

## 2. application.properties and application.yml

```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/app
```

Use YAML for nested structure or properties for simple key-value configuration. Both are supported.

## 3. Profiles

A profile selects a group of environment-specific beans or configuration.

Files:
- application.yml: shared defaults.
- application-dev.yml: development overrides.
- application-prod.yml: production overrides.

Activate a profile through environment/configuration, for example `SPRING_PROFILES_ACTIVE=dev`. Avoid hardcoding production profile activation in the application artifact.

## 4. @Profile

```java
@Configuration
@Profile("dev")
class DevConfig {
    @Bean
    Clock clock() { return Clock.systemUTC(); }
}
```

A bean annotated with @Profile is registered only when the profile expression matches.

## 5. @Value versus @ConfigurationProperties

`@Value` is useful for a small number of individual properties.

`@ConfigurationProperties` binds a group of related properties to a typed class/record and supports structured configuration and validation.

```java
@ConfigurationProperties(prefix = "payment")
@Validated
public record PaymentProperties(
    @NotBlank String baseUrl,
    @NotNull Duration timeout
) {}
```

Register configuration properties using configuration-properties scanning or explicit enablement, depending on the application setup.

## 6. Secrets and environment variables

Do not commit passwords, API keys, private keys, or production credentials to Git. Use a secrets manager or protected deployment environment variables. Environment variables are configuration delivery, not a complete secrets-management strategy by themselves.

## 7. Common mistakes

- Committing secrets into application-prod.yml.
- Assuming a profile automatically changes every property.
- Duplicating all common configuration across profiles.
- Using @Value for a large group of related settings.
- Ignoring property-source precedence.
- Confusing active profiles with Maven/Gradle build profiles.

## 8. Interview questions and answers

**Q1. What are Spring profiles?**  
A way to conditionally load beans and configuration for environments or modes such as dev, test, and prod.

**Q2. How do you activate a profile?**  
Through configuration such as spring.profiles.active, environment variables, or command-line arguments.

**Q3. @Value versus @ConfigurationProperties?**  
@Value injects individual values. @ConfigurationProperties binds a structured group into a typed object and supports validation.

**Q4. Where should secrets be stored?**  
In a secrets manager or protected runtime configuration, not in source control.

**Q5. Which config wins when values are defined in multiple places?**  
Spring Boot applies property-source precedence; higher-priority sources override lower-priority ones. Check the precedence rules for the Boot version in use.

## 9. Practice

Create dev and prod database configurations. Bind payment timeout and base URL using ConfigurationProperties, validate them, and explain how environment variables override defaults.

## 10. Quick revision

Profiles select environment-specific beans/config. Externalized configuration keeps code portable. Use ConfigurationProperties for structured settings and never commit secrets.
