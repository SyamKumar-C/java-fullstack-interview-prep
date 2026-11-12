# Spring and Spring Boot Interview Coverage Checklist

Use this checklist as a practical test. For each item, explain the concept in simple words, write a small example, describe a production use case and answer one follow-up question.

## IoC, DI and bean management
- [ ] IoC versus DI; why constructor injection is preferred
- [ ] BeanDefinition, component scanning and ApplicationContext
- [ ] `@Component`, `@Service`, `@Repository` versus `@Bean`
- [ ] `@Primary` versus `@Qualifier`; multiple implementations
- [ ] Singleton, prototype, request and session scopes
- [ ] Prototype injected into singleton; ObjectProvider/scoped proxy
- [ ] Circular dependencies and design fixes
- [ ] Singleton thread safety and stateless service design

## Bean lifecycle and AOP
- [ ] Instantiation, dependency population, post-processors, init and destroy
- [ ] `@PostConstruct`, `InitializingBean`, `@PreDestroy`
- [ ] BeanFactoryPostProcessor versus BeanPostProcessor
- [ ] Aspect, advice, join point, pointcut, target and proxy
- [ ] Before, after, after-returning, after-throwing and around advice
- [ ] JDK dynamic proxy versus CGLIB
- [ ] Self-invocation and proxy limitations
- [ ] Why `@Transactional`, `@Async` and `@Cacheable` may not run on self-call

## Configuration and Boot
- [ ] `@SpringBootApplication` and its three main annotations
- [ ] Auto-configuration, starters and conditional annotations
- [ ] `@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`
- [ ] Condition evaluation report and debugging startup
- [ ] Profiles, property precedence and externalized configuration
- [ ] `@Value` versus `@ConfigurationProperties`; validation
- [ ] Secret management; do not commit credentials
- [ ] Application events and startup lifecycle

## Transactions
- [ ] Transaction manager and proxy-based transaction interception
- [ ] ACID and local versus distributed transaction boundary
- [ ] Default rollback: RuntimeException/Error versus checked exception
- [ ] `rollbackFor`, `noRollbackFor`, caught exception and rollback-only
- [ ] REQUIRED, REQUIRES_NEW, SUPPORTS, MANDATORY, NOT_SUPPORTED, NEVER, NESTED
- [ ] Isolation levels and dirty/non-repeatable/phantom reads
- [ ] Database-specific isolation behavior (PostgreSQL/MySQL)
- [ ] readOnly, timeout, transaction boundaries and connection usage
- [ ] Self-invocation and wrong transaction manager/resource
- [ ] Why DB rollback cannot undo an external HTTP call or email

## Async, scheduling, cache and operations
- [ ] `@EnableAsync`, `@Async`, executor selection and bounded queue
- [ ] CompletableFuture composition and exception handling
- [ ] ThreadLocal/security/tenant context propagation
- [ ] `@EnableScheduling`, cron/time zone, fixed rate versus fixed delay
- [ ] Duplicate scheduled execution across replicas and distributed locking
- [ ] `@Cacheable`, `@CachePut`, `@CacheEvict`, condition and unless
- [ ] Tenant-aware cache keys, TTL and invalidation
- [ ] Actuator health, readiness, metrics and secure endpoint exposure

## REST and validation
- [ ] Controller versus service responsibilities
- [ ] Request mapping, path variables, query parameters and request body
- [ ] Bean Validation annotations and nested validation
- [ ] Global exception handling with `@ControllerAdvice`
- [ ] Filters versus HandlerInterceptors versus AOP
- [ ] Correct HTTP status codes and consistent error response shape

## Spring Security
- [ ] SecurityFilterChain and request matcher ordering
- [ ] Authentication versus authorization
- [ ] AuthenticationManager, AuthenticationProvider and SecurityContext
- [ ] Role versus authority; ROLE_ and SCOPE_ prefixes
- [ ] JWT signature, issuer, audience, expiry and key rotation
- [ ] OAuth 2.0 authorization server, client and resource server; OIDC
- [ ] Method security and proxy limitation
- [ ] CSRF versus CORS; cookie session versus bearer token
- [ ] Multi-tenant ownership checks; never trust a client tenant header alone

## Testing
- [ ] JUnit lifecycle, assertions and parameterized tests
- [ ] Mockito mock, spy, stubbing, verify and ArgumentCaptor
- [ ] Unit test versus integration test
- [ ] `@WebMvcTest`, `@DataJpaTest`, `@JsonTest`, `@SpringBootTest`
- [ ] MockMvc, validation and security tests
- [ ] Testcontainers and production database compatibility
- [ ] What should be mocked and what should remain real

## Practical scenarios to explain aloud
- [ ] Two PaymentGateway beans: choose implementation by tenant/configuration
- [ ] A method annotated `@Transactional` does not roll back: debug proxy, exception and manager
- [ ] A scheduled job runs three times: explain multi-replica scheduling
- [ ] Cache returns stale tenant data: redesign key and invalidation
- [ ] A JWT works locally but fails in production: inspect issuer, audience, key and clock
- [ ] A controller test fails because a dependency is missing: choose the correct slice/mock
