# Spring Boot Configuration and Profiles

Spring Boot reads configuration from application.properties/yml, environment variables, command-line arguments, config imports and other sources. Higher-precedence sources can override lower-precedence ones.

```yaml
app:
  payment:
    timeout: 3s
```

Use @ConfigurationProperties for structured, type-safe configuration and validation. @Value is useful for individual values.

Profiles select environment-specific beans/configuration. Use application-dev.yml and application-prod.yml, but keep credentials outside source control. Inject secrets through a secret manager or deployment environment.

**Interview:** @Value vs @ConfigurationProperties? Individual property injection vs binding a structured group of settings.
