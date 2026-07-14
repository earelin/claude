---
name: java-integration-test
description: Use when editing a Java integration test.
---

# Write a Java integration test

Write integration tests that verify how Java code interacts with **external processes**:
databases, cache servers, message brokers, outbound HTTP clients, and the application's own
controllers (the surface external processes connect to). Spin real dependencies with
**Testcontainers**, assert with **AssertJ** (and **AssertJ DB** for database state), name test
methods in **snake_case**, and keep them in a dedicated **integration** source set.

## Scope — what an integration test covers

- **Only interactions that cross a process boundary.** A test earns the integration label when
  it exercises a real external dependency: a database, cache server, broker, an outbound HTTP
  client hitting a stubbed remote, or the application's own controller as reached over HTTP.
- **Not business logic in isolation** — that belongs in a unit test (see `java-unit-test`). Do
  not re-test pure logic here; test that the wiring to the outside world behaves.
- **Pick the narrowest boundary that answers the question.** A repository test needs only a
  real database, not the whole application; a controller test needs a web server but its
  service collaborators mocked.

## Before you write

1. Read the project's build file (`pom.xml` / `build.gradle`) and existing integration tests —
   the repo's own source-set layout, container setup, and conventions win over this skill.
2. Confirm the **integration source set** exists (e.g. `src/integrationTest/java`, or the
   project's equivalent) and that Testcontainers, AssertJ, AssertJ DB, and REST-assured are on
   its classpath. If the source set or a dependency is missing, set it up (or tell the user)
   before writing tests.
3. Identify the external process under test and the narrowest slice that exercises it.

## Rules

- **Live in the integration source set.** Never place these under `src/test` next to unit
  tests — use the project's separate integration source set (`src/integrationTest/...` or
  equivalent) with its own Gradle/Maven task so they run apart from the fast unit suite.
- **Real dependencies run in Testcontainers.** Start databases, caches, brokers, and other
  backing services as containers (`@Container`, `@Testcontainers`, or the framework's
  Testcontainers integration). Never hit a shared or developer-local instance; each run must be
  hermetic and disposable.
- **Assert with AssertJ; assert database state with AssertJ DB.** Use `assertThat(...)` for
  in-memory values, and AssertJ DB (`Table`, `Request`, `assertThat(table).row(0)...`) to
  assert rows, columns, and values written to or read from the database rather than round-
  tripping through the code under test.
- **Test controllers with a real web server but mocked collaborators.** Use the framework's
  own feature to spin an HTTP server (e.g. Spring Boot's `@SpringBootTest(webEnvironment =
  RANDOM_PORT)` / `@MicronautTest`), **mock the controller's service dependencies** (so only
  the HTTP layer — routing, serialization, status codes, validation — is under test), and
  drive endpoints with **REST-assured** (`given()...when().get(...).then().statusCode(...)`).
- **Mock with strict stubbing; never lenient, never `verify(...)`.** When you mock a
  controller's collaborators, stub only what the endpoint needs with `when(...).thenReturn(...)`
  / `.thenThrow(...)` and assert the HTTP response. Do not relax strictness with `lenient()` or
  `Strictness.LENIENT`, and do not add `verify(...)` — a strict stub already fails the test if
  the collaborator is never called, and the response body/status is what the test asserts on.
- **Test method names are snake_case** describing the interaction — e.g.
  `persists_order_and_assigns_generated_id`, `returns_404_when_order_is_unknown`,
  `caches_lookup_result_for_configured_ttl`. No `test` prefix.
- **Keep tests deterministic and isolated.** Reset or recreate external state between tests
  (clean schema, flush cache); do not depend on execution order or leftover data. Prefer one
  container lifecycle per class and clean data per test.

## Format

Repository test against a real database (Testcontainers + AssertJ DB):

```java
@Testcontainers
class OrderRepositoryIntegrationTest {

  @Container
  static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

  private DataSource dataSource; // pointed at postgres.getJdbcUrl()
  private OrderRepository orderRepository;

  @Test
  void persists_order_and_assigns_generated_id() {
    // when
    long id = orderRepository.save(new Order("c-1", 25));

    // then — assert the row directly with AssertJ DB
    Table orders = new Table(dataSource, "orders");
    assertThat(orders).row(0)
        .value("id").isEqualTo(id)
        .value("customer_id").isEqualTo("c-1")
        .value("amount").isEqualTo(25);
  }
}
```

Controller test (framework web server up, dependencies mocked, driven with REST-assured):

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class OrderControllerIntegrationTest {

  @LocalServerPort
  int port;

  @MockBean
  OrderService orderService; // controller collaborator is mocked

  @Test
  void returns_order_as_json_when_it_exists() {
    when(orderService.findById("o-1")).thenReturn(new Order("c-1", 25));

    given()
        .port(port)
    .when()
        .get("/orders/o-1")
    .then()
        .statusCode(200)
        .body("customerId", equalTo("c-1"))
        .body("amount", equalTo(25));
  }

  @Test
  void returns_404_when_order_is_unknown() {
    when(orderService.findById("nope")).thenThrow(new OrderNotFoundException());

    given().port(port)
    .when().get("/orders/nope")
    .then().statusCode(404);
  }
}
```

## Steps

1. Confirm the integration source set and required dependencies exist; set them up first if not.
2. Identify the external process under test and pick the narrowest boundary — a repository +
   database, a client + stubbed remote, or a controller + web server with mocked services.
3. Stand up the real dependency in a Testcontainers container (or the framework web server for
   controllers, mocking the controller's collaborators).
4. Write snake_case `@Test`s: exercise the interaction, then assert with AssertJ — AssertJ DB
   for database state, REST-assured expectations for controller responses.
5. Run the integration task (e.g. `gradle integrationTest` / `mvn verify`) to confirm the
   tests pass, and summarise the interactions covered for the user.
