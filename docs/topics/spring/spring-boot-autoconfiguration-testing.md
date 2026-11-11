# Spring Boot Auto-Configuration and Testing

> Priority: P1 | Level: Intermediate | Study time: 45 minutes

## 1. Short interview answer
Spring Boot auto-configuration creates beans based on classpath libraries, existing beans, and configuration properties. Conditional annotations decide whether defaults apply. Testing can load only the required slice or the full application context.

## 2. How auto-configuration works
1. `@SpringBootApplication` enables component scanning and auto-configuration.
2. Boot discovers auto-configuration candidates.
3. Conditions check classes, properties, and existing beans.
4. Matching bean definitions are registered.
5. A user-defined bean can make a conditional default back off.

Important conditions:
- `@ConditionalOnClass`: required class exists.
- `@ConditionalOnMissingBean`: no matching bean exists.
- `@ConditionalOnProperty`: a property matches.
- `@ConditionalOnBean`: another bean exists.

## 3. Example

```java
@Configuration
@ConditionalOnProperty(name = "feature.audit.enabled", havingValue = "true")
class AuditConfiguration {
    @Bean
    @ConditionalOnMissingBean
    AuditPublisher auditPublisher() {
        return new DefaultAuditPublisher();
    }
}
```

The configuration is skipped unless the property is true. If the application supplies its own AuditPublisher, the default bean backs off.

## 4. Typed configuration

```java
@ConfigurationProperties(prefix = "app.storage")
@Validated
public record StorageProperties(
    @NotBlank String bucket,
    @Min(1) int maxUploadMb
) {}
```

Use `@ConfigurationPropertiesScan` or `@EnableConfigurationProperties` to register it. Typed properties group related settings and support validation. Keep secrets outside source control.

## 5. Testing levels

| Test | Context | Use |
|---|---|---|
| Unit test | Class and mocks | Business rules |
| `@WebMvcTest` | MVC slice | Controller mapping and validation |
| `@DataJpaTest` | JPA slice | Repository and mapping behavior |
| `@SpringBootTest` | Full application | Wiring and integration |

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

Use the mock-bean annotation supported by the project's Spring version. An in-memory database test does not prove that production-database-specific SQL behaves the same.

## 6. Common mistakes
- Assuming every auto-configuration always creates a bean.
- Defining duplicate beans without understanding conditional back-off.
- Using full-context tests for every small unit.
- Putting passwords or API keys in committed YAML.
- Assuming a test slice loads every application bean.

## 7. Practical interview questions
**Q1. What does auto-configuration use?** Classpath, properties, and existing bean conditions.

**Q2. How can a custom bean replace a default?** A default guarded by ConditionalOnMissingBean backs off when a matching user bean exists.

**Q3. `@SpringBootTest` vs `@WebMvcTest`?** Full application context versus a focused MVC slice.

**Q4. Why use ConfigurationProperties?** Typed, grouped, validated external configuration.

**Q5. Does auto-configuration remove the need for configuration?** No. It provides defaults; business and environment-specific choices remain.

## 8. Practice
1. Create a property-controlled bean and override it in a test.
2. Test a controller with MockMvc.
3. Test a repository with DataJpaTest.
4. Explain why production DB integration tests are still useful.
