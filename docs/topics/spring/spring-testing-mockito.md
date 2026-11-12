# Spring Boot Testing, JUnit and Mockito

## 1. Short interview answer
Unit tests check a small unit in isolation. Integration tests check how components work together with real or representative infrastructure. JUnit provides test execution and assertions; Mockito creates test doubles. Spring Boot test slices load a focused part of the context, while `@SpringBootTest` loads a broad application context.

## 2. JUnit 5 essentials
Know `@Test`, `@BeforeEach`, `@AfterEach`, `@Nested`, `@DisplayName`, `assertThrows`, parameterized tests and lifecycle behavior. Keep each test focused on one behavior and use descriptive names.

```java
@ExtendWith(MockitoExtension.class)
class PriceServiceTest {
    @Mock PriceRepository repository;
    @InjectMocks PriceService service;

    @Test
    void appliesDiscount() {
        given(repository.findPrice(10L))
            .willReturn(new BigDecimal("100.00"));

        BigDecimal result = service.priceAfterDiscount(10L);

        assertEquals(new BigDecimal("90.00"), result);
    }
}
```

## 3. Mockito essentials
- `@Mock`: creates a mock object.
- `@InjectMocks`: creates the class under test and injects available mocks.
- `when(...).thenReturn(...)` / `given(...).willReturn(...)`: stubs behavior.
- `verify(...)`: checks an interaction.
- `ArgumentCaptor`: captures an argument passed to a mock.
- `doThrow(...)`: useful for void methods.
- Spy: wraps a real object; real methods run unless stubbed.

Mock external boundaries, not every internal method. Excessive interaction verification makes tests brittle and tied to implementation details.

## 4. Test layers

| Annotation/tool | Context | Best use |
|---|---|---|
| MockitoExtension | No Spring context | Service/business unit tests |
| `@WebMvcTest` | MVC slice | Controllers, binding, validation, filters |
| `@DataJpaTest` | JPA slice | Repository queries and entity mappings |
| `@JsonTest` | JSON slice | Serialization contracts |
| `@SpringBootTest` | Full application context | Wiring and integration |
| Testcontainers | Real dependency in container | Database/broker integration |

Use `@AutoConfigureMockMvc` with `@SpringBootTest` when you need a full context and MockMvc. Use a real HTTP server test when network/server behavior itself matters.

## 5. Controller test
```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {
    @Autowired MockMvc mvc;
    @MockitoBean OrderService service;

    @Test
    void returnsNotFoundWhenMissing() throws Exception {
        given(service.find(99L)).willThrow(new OrderNotFoundException());
        mvc.perform(get("/orders/99"))
            .andExpect(status().isNotFound());
    }
}
```

`@MockitoBean` is available in newer Spring Framework versions; older Boot versions commonly use `@MockBean`. Match the annotation to the project's dependency version.

## 6. What should be tested?
- Happy path and boundary values.
- Invalid input and validation messages.
- Authentication and authorization failures.
- Not found, conflict and duplicate requests.
- Repository failures and transaction rollback.
- Serialization compatibility for public APIs.
- Retry/idempotency behavior where relevant.

Test public behavior rather than private methods. A test should fail when behavior is wrong, not merely when implementation changes.

## 7. Database tests and Testcontainers
An embedded database is convenient but may differ from production in SQL dialect, locking, JSON support, indexes, collation and query planning. Testcontainers can start the same database engine used in production, reducing those differences. It does not remove the need to control test data, schema migrations and cleanup.

## 8. Common mistakes
- Using mocks to test the repository query itself.
- Using only H2 for PostgreSQL-specific behavior.
- Starting the full Spring context for every unit test.
- Verifying every internal method call.
- Sharing mutable test data across test cases.
- Ignoring test isolation and execution order.
- Mocking the class under test instead of its collaborators.

## 9. Interview questions
**Q1. Unit vs integration test?** Isolated logic versus collaboration among components/infrastructure.

**Q2. Mock vs spy?** A mock supplies configured behavior; a spy wraps a real object and may execute real methods.

**Q3. When use WebMvcTest?** To test controller/MVC behavior without starting the whole application.

**Q4. Why Testcontainers?** To exercise code against a real database/broker engine in a repeatable test environment.

**Q5. Should private methods be tested directly?** Usually no; test their behavior through public methods.

## Practice
1. Test a service's success, validation failure and repository exception.
2. Test controller status codes and request validation using MockMvc.
3. Verify a repository query against PostgreSQL with Testcontainers.
4. Write parameterized tests for boundary values.
