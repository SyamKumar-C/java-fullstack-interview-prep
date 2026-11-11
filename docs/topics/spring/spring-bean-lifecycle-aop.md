# Spring Bean Lifecycle and AOP

Simplified lifecycle: instantiate → inject dependencies → aware callbacks → BeanPostProcessor before-init → initialization callbacks (@PostConstruct/init method) → post-init callbacks (proxy may be exposed) → destruction callbacks on shutdown for supported scopes.

## AOP terms
Aspect: cross-cutting module. Advice: code executed at a join point. Pointcut: selects join points. Join point: method execution in Spring proxy-based AOP. Proxy: intercepts calls to target.

Spring commonly uses JDK dynamic proxies for interfaces or CGLIB subclass proxies. Self-invocation through this bypasses the proxy, so @Transactional and similar advice may not run on internal calls.

**Interview:** Why can self-invocation bypass @Transactional? The call does not pass through the Spring proxy.
