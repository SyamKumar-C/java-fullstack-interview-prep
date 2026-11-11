# Spring Bean Lifecycle and AOP

## 1. Bean lifecycle overview

A Spring bean is created and managed by the IoC container. A simplified lifecycle is:

1. Instantiate the bean.
2. Inject dependencies and populate properties.
3. Run aware callbacks where applicable.
4. Run BeanPostProcessor before-initialization callbacks.
5. Run initialization callbacks such as @PostConstruct, InitializingBean.afterPropertiesSet or a custom init method.
6. Run BeanPostProcessor after-initialization callbacks; this is where proxy wrapping may occur.
7. Bean is ready for use.
8. On context shutdown, run destruction callbacks such as @PreDestroy, DisposableBean.destroy or a custom destroy method.

Exact details vary by bean type and container setup. Prototype-scoped beans are not fully managed for destruction in the same way as singleton beans.

## 2. Bean scopes

- singleton: one instance per bean definition per Spring IoC container; default.
- prototype: new instance each time requested from the container.
- request/session: web scopes tied to an HTTP request/session.
- application/websocket: web-specific scopes.

Singleton does not mean one instance across the whole JVM or across application servers.

## 3. BeanPostProcessor

A BeanPostProcessor can inspect or wrap beans before/after initialization. Framework features such as proxies rely on post-processing infrastructure.

## 4. AOP

Aspect-oriented programming separates cross-cutting concerns such as transactions, logging, metrics and security from business methods.

Key terms:
- Aspect: module for a cross-cutting concern.
- Advice: code executed at a selected point.
- Join point: execution point; in Spring proxy AOP this is typically method execution.
- Pointcut: expression selecting join points.
- Target: object being advised.
- Proxy: wrapper through which advice is applied.

## 5. Advice types

- Before: runs before the method.
- After returning: runs after normal return.
- After throwing: runs when an exception escapes.
- After: runs after completion, success or failure.
- Around: controls whether/when the target method proceeds.

## 6. Proxy limitations

Spring AOP commonly uses JDK dynamic proxies for interfaces or CGLIB subclass proxies. Calls must pass through the proxy. Self-invocation such as this.otherMethod() bypasses proxy advice. Private/final methods also have limitations with subclass-based proxies.

## 7. Practical scenario

@Transactional is implemented using transaction advice. A call from another Spring bean passes through the proxy; a self-call inside the same object usually does not.

## 8. Common mistakes

- Assuming singleton means one instance across all containers.
- Expecting prototype destruction callbacks to be fully automatic.
- Expecting self-invocation to trigger AOP.
- Putting important business logic only in advice.
- Using broad pointcuts that intercept unintended methods.

## 9. Interview questions

**Q1. What are the main bean lifecycle stages?** Instantiation, dependency injection, aware callbacks, post-processing, initialization, use and destruction.

**Q2. What is BeanPostProcessor?** An extension point that can modify or wrap bean instances around initialization.

**Q3. What is AOP used for?** Cross-cutting behavior such as transactions, security and logging.

**Q4. Why does self-invocation bypass Spring AOP?** The call uses this directly rather than passing through the Spring proxy.

**Q5. JDK proxy vs CGLIB?** JDK proxies implement interfaces; CGLIB creates a subclass proxy, subject to class/method limitations.

## 10. Practice

Create a bean with constructor, @PostConstruct and @PreDestroy logging. Add a transactional method and demonstrate external proxy invocation versus self-invocation.
