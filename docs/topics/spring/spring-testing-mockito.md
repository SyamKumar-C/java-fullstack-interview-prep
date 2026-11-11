# Spring Boot Testing, JUnit and Mockito

> Priority: P1 | Level: Intermediate | Study time: 55 minutes

## 1. Short interview answer
Unit tests verify a small unit in isolation; integration tests verify collaboration between components and infrastructure. JUnit provides test lifecycle and assertions. Mockito creates test doubles and verifies interactions. Spring Boot test slices load only selected application layers, while SpringBootTest loads a broader context.

## 2. JUnit basics

```java
@ExtendWith(MockitoExtension.class)
class PriceServiceTest {
    @Mock PriceRepository repository;
    @InjectMocks PriceService service;

    @Test
    void appliesDiscount() {
        given(repository.findPrice(10L)).willReturn(new BigDecimal("100"));
        BigDecimal result = service.priceAfterDiscount(10L);
        assertEquals(new BigDecimal("90.00"), result);
    }
}
```

Use setup methods for shared fixture creation, but keep each test's behavior obvious. Parameterized tests are useful for multiple input/output cases.

## 3. Mockito essentials
- `@Mock`: creates a mock.
- `@InjectMocks`: creates the class under test and injects available mocks.
- `when(...).thenReturn(...)` or BDD `given(...).willReturn(...)`: stubs behavior.
- `verify(...)`: checks an interaction.
- `ArgumentCaptor`: captures an argument passed to a mock.
- `doThrow(...)`: useful for stubbing void methods.

Mock external boundaries; avoid mocking every internal class. Over-mocking makes tests mirror implementation rather than behavior.

## 4. Spring test slices
- `@WebMvcTest`: MVC controllers, filters/configuration relevant to the slice.
- `@DataJpaTest`: JPA repositories and mappings.
- `@JsonTest`: JSON serialization.
- `@SpringBootTest`: broader/full application context.
- `@Testcontainers`: run real dependencies such as PostgreSQL or Kafka in containers when configured.

A slice test is faster and more focused. A full-context test checks wiring but is slower.

## 5. Controller test example

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

Use the mock-bean annotation supported by the project's Spring version. Test request validation, status, response body, and security rules where relevant.

## 6. What to test
- Happy path.
- Boundary values and invalid input.
- Authorization failures.
- Not-found and conflict cases.
- Dependency failures and retry behavior.
- Transaction rollback and persistence constraints.
- Serialization compatibility for public APIs.

Avoid testing private methods directly. Test observable behavior through public contracts.

## 7. Common mistakes
- Using only mocks and never testing SQL against a real database.
- Using full Spring context for every unit test.
- Verifying every internal method call.
- Sharing mutable test fixtures across tests.
- Ignoring test data cleanup and execution order.
- Assuming an H2 test proves PostgreSQL behavior.

## 8. Interview questions
**Q1. Unit vs integration test?** Unit tests isolate a small unit; integration tests verify collaboration with real or representative dependencies.

**Q2. Mock vs spy?** A mock is a test double with configured behavior; a spy wraps a real object and can call real methods unless stubbed.

**Q3. When use WebMvcTest?** To test MVC/controller behavior without loading the entire application.

**Q4. Why Testcontainers?** It can run the real database/broker engine for more realistic integration tests.

**Q5. Should private methods be tested directly?** Usually no; test public behavior and refactor if logic is hard to test.

## 9. Practice
1. Unit-test a service with success and repository failure.
2. Test request validation using MockMvc.
3. Test a repository against PostgreSQL in Testcontainers.
4. Write a parameterized test for boundary inputs.
