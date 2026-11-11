# GraphQL Fundamentals, Spring GraphQL and Federation

> Priority: P2 | Level: Intermediate | Study time: 55 minutes

## 1. Short interview answer
GraphQL is a query language and runtime for APIs. A schema defines types and operations; queries read data, mutations change data, and subscriptions can stream updates. Clients select the fields they need. Federation composes multiple GraphQL subgraphs into a graph, while each subgraph owns part of the schema and its data.

## 2. Schema basics

```graphql
type Query {
  order(id: ID!): Order
}

type Mutation {
  createOrder(input: CreateOrderInput!): Order!
}

type Order {
  id: ID!
  status: String!
  total: Float!
}
```

Resolvers connect schema fields to application services. Keep business rules in service/domain layers rather than embedding them in resolver methods.

## 3. Query and mutation
A query reads data:

```graphql
query {
  order(id: "42") {
    id
    status
    total
  }
}
```

A mutation changes data:

```graphql
mutation {
  createOrder(input: { customerId: "7" }) {
    id
    status
  }
}
```

GraphQL does not automatically make requests faster. Poor resolver design can create N+1 database calls. Use batching/data-loader patterns and request-level caching where appropriate.

## 4. Spring GraphQL
Spring for GraphQL maps schema fields to controller methods using annotations such as `@QueryMapping`, `@MutationMapping`, and `@SchemaMapping`.

```java
@Controller
class OrderGraphqlController {
    private final OrderService service;

    OrderGraphqlController(OrderService service) {
        this.service = service;
    }

    @QueryMapping
    OrderDto order(@Argument Long id) {
        return service.find(id);
    }
}
```

Validate inputs and enforce authorization in the backend service boundary. Avoid exposing JPA entities directly as the public GraphQL schema.

## 5. Federation concepts
In a federated graph:
- Subgraphs own schema portions and resolvers.
- A gateway/router composes subgraph schemas and routes operations.
- Entity keys identify shared entities across subgraphs.
- Ownership should be explicit; do not let multiple subgraphs independently own the same field.
- Cross-subgraph calls add network latency and failure paths.

Federation is useful when teams need independent schema ownership while clients need one graph. It adds composition, versioning, observability, and operational complexity.

## 6. GraphQL vs REST

| GraphQL | REST |
|---|---|
| Client selects fields | Server defines resource representation |
| Often one graph endpoint | Resource-oriented endpoints |
| Schema-driven typed operations | OpenAPI commonly documents HTTP contracts |
| Can reduce over/under-fetching | Easier HTTP caching patterns in many cases |
| Resolver N+1 risk | Endpoint/query design can also create N+1 |

Choose based on client needs, team ownership, caching, tooling, and operational costs.

## 7. Security and performance
- Limit query depth and complexity.
- Set request timeouts and response-size limits.
- Apply authorization at field/service level where needed.
- Avoid exposing introspection inappropriately for the deployment context.
- Batch related loads to avoid N+1.
- Log operation names and timing without logging sensitive variables.

## 8. Interview questions
**Q1. Query vs mutation?** Query reads; mutation changes state.

**Q2. What is a resolver?** Code that supplies a value for a schema field.

**Q3. What is N+1 in GraphQL?** Resolving a list and then issuing one additional query per item for a related field.

**Q4. What is federation?** Composition of independently owned GraphQL subgraphs into one graph.

**Q5. Does GraphQL replace REST in every system?** No. It is one API design choice with trade-offs.

## 9. Practice
1. Define schema for customer and order.
2. Implement a query mapping with service delegation.
3. Explain how DataLoader prevents N+1 calls.
4. Draw a gateway with order and customer subgraphs.
