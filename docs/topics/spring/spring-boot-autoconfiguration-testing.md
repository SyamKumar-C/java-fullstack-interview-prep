# Spring Boot Auto-Configuration and Testing

## 1. Short interview answer
Spring Boot auto-configuration provides sensible bean defaults based on the classpath, existing beans and configuration properties. Conditional rules decide whether each configuration applies. Tests should use the smallest context that proves the behavior: unit test, slice test, or full integration test.

## 2. What @SpringBootApplication combines
It combines:
- `@SpringBootConfiguration`: a specialized configuration class.
- `@EnableAutoConfiguration`: imports Boot auto-configuration candidates.
- `@ComponentScan`: scans from the application's package downward by default.

Keep the main application class in a root package above the application's components and configuration.

## 3. How auto-configuration works
1. Boot discovers auto-configuration candidates supplied by Boot and libraries.
2. Conditional annotations evaluate classpath, properties, beans and environment.
3. Matching bean definitions are registered.
4. User-defined beans can cause conditional defaults to back off.
5. The application can override or exclude selected auto-configurations.

Common conditions:
- `@ConditionalOnClass`: a class is available.
- `@ConditionalOnMissingBean`: no matching bean exists.
- `@ConditionalOnBean`: another bean exists.
- `@ConditionalOnProperty`: a property has a required value.
- `@ConditionalOnWebApplication`: application has a web environment.

## 4. Example custom auto-configuration
```java
@AutoConfiguration
@ConditionalOnClass(AuditClient.class)
@ConditionalOnProperty(
    prefix = "app.audit", name = "enabled",
    havingValue = "true", matchIfMissing = false)
class AuditAutoConfiguration {
    @Bean
    @ConditionalOnMissingBean
    AuditClient auditClient(AuditProperties properties) {
        return new HttpAuditClient(properties.endpoint());
    }
}
```

For a reusable Boot 3 auto-configuration library, register the configuration in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`. Follow the registration mechanism for the Boot version being used.

## 5. How to debug auto-configuration
- Start with the condition evaluation report (for example, run with `--debug`).
- Use Actuator's conditions endpoint only if it is enabled and safely exposed.
- Check missing classes, property spelling/value, active profiles and existing beans.
- Inspect which configuration created a bean before adding another bean of the same type.

## 6. Externalized configuration
Use `@ConfigurationProperties` for a group of related settings and validation. Use `@Value` for a small individual value. Do not commit secrets. Environment variables and command-line arguments can override file-based defaults according to Boot's property-source precedence.

## 7. Testing pyramid and Spring test choices

| Test type | Typical tool | What it proves |
|---|---|---|
| Unit | JUnit + Mockito | Business logic with collaborators controlled |
| MVC slice | `@WebMvcTest` + MockMvc | Mapping, validation, serialization and MVC behavior |
| JPA slice | `@DataJpaTest` | Repository queries and mappings |
| JSON slice | `@JsonTest` | JSON serialization/deserialization |
| Full context | `@SpringBootTest` | Application wiring and integration |

Prefer unit tests for business rules; use slices for framework behavior; reserve full-context tests for wiring and end-to-end integration.

## 8. MVC slice example
```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {
    @Autowired MockMvc mvc;
    @MockitoBean OrderService service;

    @Test
    void returnsOrder() throws Exception {
        given(service.find(7L)).willReturn(new OrderDto(7L, "PAID"));
        mvc.perform(get("/orders/7"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.status").value("PAID"));
    }
}
```

Use `@MockitoBean` with Spring Framework versions that provide it. Older Spring Boot projects commonly use Boot's `@MockBean`; check the project's dependency version rather than copying an annotation blindly.

## 9. Testing database behavior
`@DataJpaTest` often configures an embedded database when one is available. This is fast, but H2 behavior is not identical to PostgreSQL or MySQL. For database-specific SQL, locking, JSON columns, collations or query plans, use integration tests against the real engine, often with Testcontainers.

## 10. Common mistakes
- Assuming auto-configuration always creates a bean.
- Forgetting that user beans may make defaults back off.
- Putting the main application class in a package that misses components.
- Using `@SpringBootTest` for every small test.
- Believing H2 proves production database behavior.
- Mocking repositories in a test that is supposed to verify the query itself.

## 11. Interview questions
**Q1. What drives auto-configuration?** Classpath, environment properties, web/application type and existing beans.

**Q2. How does a custom bean replace a default?** A default guarded by `@ConditionalOnMissingBean` backs off when a matching bean exists.

**Q3. What is the condition evaluation report?** A diagnostic report showing why auto-configurations matched or did not match.

**Q4. `@SpringBootTest` vs `@WebMvcTest`?** Full application context versus focused MVC slice.

**Q5. Why use Testcontainers?** To test against a real database or broker engine in a repeatable container.

## Practice
1. Create a property-controlled configuration and a default bean that backs off.
2. Start the app with debug condition reporting and explain one positive and one negative match.
3. Test a controller with MockMvc and a service mock.
4. Test a repository query against PostgreSQL using Testcontainers.
