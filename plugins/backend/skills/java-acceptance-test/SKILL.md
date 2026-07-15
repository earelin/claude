---
name: java-acceptance-test
description: Write Java acceptance tests that drive a real running instance of the whole application as a black box over the wire — no framework test runner — with downstream services mocked by standalone stub servers (WireMock or similar), state pinned to fixed datasets and fixtures, endpoints driven by REST-assured, and assertions in AssertJ. Use whenever writing, adding, scaffolding, reviewing, or fixing a Java acceptance or end-to-end test, or testing a high-value user scenario against a deployed application. For business logic in isolation use java-unit-test; for a single external interaction such as a repository or controller use java-integration-test.
paths:
  - "**/acceptance/src/test/java/**/*Test.java"
---

# Write a Java acceptance test

Write acceptance tests that exercise a **real, running instance of the whole application** as a
user would, through its outer edge — as a **black box**, over the wire, with **no framework
test runner booting it in-process**. Make results predictable by **mocking every downstream
service** and pinning all state to **fixed database datasets and file fixtures**. Assert with
**AssertJ**, drive HTTP APIs with **REST-assured**, name test methods in **snake_case**, and
keep the suite in its **own module or dedicated source set**. These sit at the **top of the
testing pyramid**: few in number, each earning its place by covering a **high-value user
scenario**, not a code path.

## Scope — what an acceptance test covers

- **A real running instance, treated as a black box.** The application runs as a deployed
  process — its actual artifact, started the way production starts it (its packaged jar,
  container image, or `docker-compose` stack) — and the test connects to it from the outside
  over its public surface (HTTP API, message queue, CLI). The test does **not** boot the app
  with a framework test runner (`@SpringBootTest`, `@MicronautTest`, etc.) and never reaches
  inside to call a service or repository directly; it knows only the app's public contract and
  a base URL.
- **A high-value user scenario, end to end.** Each test tells a business story a stakeholder
  cares about — "a customer places an order and receives confirmation", "an unpaid invoice is
  rejected" — not a branch or an edge case. Edge cases, boundaries, and error branches belong
  in unit tests (see `java-unit-test`); single external interactions belong in integration
  tests (see `java-integration-test`). Do not re-test those here.
- **Deliberately few.** As the pyramid's tip, acceptance tests are slow and broad, so keep them
  scarce. If you are tempted to add a variation, ask whether a unit test would cover it — it
  usually does.

## What is real and what is faked

- **Real:** the application itself, in full — its routing, serialization, validation, business
  logic, and persistence wiring — running as its own deployed process, exactly as it ships.
- **Faked for predictability:**
  - **Downstream services are mocked.** Every outbound call to a service you do not own —
    third-party HTTP APIs, payment gateways, mail, other microservices — is served by a
    **standalone stub server** (WireMock/MockServer running as its own process) that the
    application instance is **configured to point at**, returning canned, deterministic
    responses. No real network calls leave the environment. The stub is external to the app,
    not an injected in-process mock.
  - **The database is seeded with a fixed dataset.** Load a known, version-controlled dataset
    into the running instance's database before the scenario (SQL script, Flyway/Liquibase
    seed, or a dataset tool run against its connection) so every run starts from the same rows.
    Prefer the same database engine the app uses in production; keep the *data* fixed.
  - **Files come from fixtures.** Read inputs from checked-in fixture files and write to a
    temporary directory; never depend on ambient filesystem state.

## Before you write

1. Read the project's build file (`pom.xml` / `build.gradle`) and any existing acceptance
   tests — the repo's own module/source-set layout, the mechanism that **starts the instance**
   (`docker-compose`, a Testcontainers app-image container, a `bootRun`/`start` build task, or
   a deployed environment), and fixture conventions win over this skill.
2. Confirm the **acceptance module or source set** exists (see the sizing rule below) and that
   AssertJ, REST-assured, and the downstream-mocking library (e.g. WireMock) are on its
   classpath. If the module/source set or a dependency is missing, set it up (or tell the user)
   before writing tests.
3. Find how the running instance's **base URL** and the stub servers' addresses are supplied to
   the tests (system property, environment variable, or a fixed local port), so REST-assured
   and the seeding step can reach them.
4. Identify the **user scenario** worth an acceptance test and, working outward from it, list
   every downstream service to mock and every piece of state (database rows, files) to pin.

## Where acceptance tests live

- **Always separate from unit and integration tests** — their own module or dedicated source
  set with its own build task, so the slow suite runs apart from the fast ones.
- **Small application → an independent module.** A single-deployable app puts its acceptance
  tests in one dedicated module (e.g. `acceptance-tests/`) that has **no compile dependency on
  the application's code** — it only knows the app's public contract and reaches it over the
  wire, against the instance started for the run.
- **Large application with bounded-context modules → a dedicated source set per module.** When
  the app is split into bounded-context modules, give each module its own acceptance source set
  (e.g. `src/acceptanceTest/java`) so each context's scenarios run against a running instance of
  that context.

## Rules

- **Run against a real running instance; no framework test runner.** The application is started
  as its own process (container/`docker-compose`/deployed env) *before* the test connects — do
  **not** use `@SpringBootTest`, `@MicronautTest`, `@MockBean`, or any in-process test harness.
  The test holds only the instance's base URL and drives it through its public entry points.
  Point REST-assured at that base URL (e.g. `RestAssured.baseURI` / `.port` from a system
  property or environment variable), not at an in-JVM port.
- **Mock every downstream service as an external stub.** Configure the running application to
  point its outbound clients at a standalone stub server (WireMock/MockServer running as its
  own process) that returns fixed responses. A run must never depend on a real remote being up
  or on its live data. Verify recorded requests against the stub server, not against an
  in-process mock.
- **Pin all state to fixed datasets and fixtures.** Seed the database from a known dataset
  before each scenario and reset it after, so tests are deterministic and order-independent.
  Source files from checked-in fixtures. Inject clocks / fixed IDs rather than reading `now()`
  or random.
- **Drive HTTP APIs with REST-assured.** Use `given()...when().post(...).then().statusCode(...)`
  to exercise endpoints, and assert on status, headers, and the response body.
- **Assert with AssertJ.** Use fluent `assertThat(...)` for values you read back (e.g. a
  follow-up GET's deserialized body, the state of a fixture file). Reserve REST-assured's own
  `body(...)` matchers for inline response-body checks; prefer AssertJ for richer object
  assertions.
- **Test method names are snake_case** telling the scenario — e.g.
  `customer_places_order_and_receives_confirmation`,
  `checkout_is_rejected_when_payment_gateway_declines`. No `test` prefix. Add `@DisplayName`
  only when a full sentence adds value.
- **One user scenario per test.** Structure it Given (seed data + stub downstreams) / When
  (drive the app through its edge) / Then (assert the user-visible outcome *and*, where it
  matters, the recorded downstream interaction).

## Format

Acceptance test against an **already-running instance**: plain JUnit (no framework test
runner), REST-assured pointed at the instance's base URL from configuration, downstream mocked
by an external WireMock server the app is configured to call, database seeded with a fixed
dataset, asserted with AssertJ.

```java
class PlaceOrderAcceptanceTest {

  // the app is started separately (docker-compose / container / deployed env);
  // the test only receives its address and the stub server's address as configuration.
  static final String BASE_URL = System.getProperty("app.baseUrl"); // e.g. http://localhost:8080
  static final String PAYMENT_STUB =
      System.getProperty("paymentStub.baseUrl"); // e.g. http://localhost:9999

  // external stub the running application is configured to call for payments
  static WireMock paymentGateway;

  @BeforeAll
  static void connect() {
    RestAssured.baseURI = BASE_URL; // black-box: drive it over the wire
    paymentGateway =
        new WireMock(URI.create(PAYMENT_STUB).getHost(), URI.create(PAYMENT_STUB).getPort());
  }

  @BeforeEach
  void seed_fixed_dataset() {
    // load the known dataset into the running instance's database, then reset the stub
    DatasetSupport.load("/datasets/catalog.sql");
    paymentGateway.resetMappings();
  }

  @Test
  void customer_places_order_and_receives_confirmation() {
    // given — the downstream payment gateway approves the charge
    paymentGateway.register(post(urlEqualTo("/charges"))
        .willReturn(okJson("{ \"status\": \"APPROVED\", \"id\": \"pay-1\" }")));

    // when — drive the whole running application through its public HTTP API
    String orderId =
        given()
            .contentType("application/json")
            .body("{ \"sku\": \"BOOK-1\", \"quantity\": 2 }")
        .when()
            .post("/orders")
        .then()
            .statusCode(201)
            .extract().path("id");

    // then — the user-visible outcome: the order is confirmed and readable back
    OrderView order =
        when().get("/orders/{id}", orderId)
        .then().statusCode(200)
        .extract().as(OrderView.class);

    assertThat(order.status()).isEqualTo("CONFIRMED");
    assertThat(order.lines()).extracting(Line::sku).containsExactly("BOOK-1");

    // and the downstream was actually asked to charge the customer
    paymentGateway.verifyThat(postRequestedFor(urlEqualTo("/charges")));
  }

  @Test
  void checkout_is_rejected_when_payment_gateway_declines() {
    paymentGateway.register(post(urlEqualTo("/charges"))
        .willReturn(okJson("{ \"status\": \"DECLINED\" }")));

    given()
        .contentType("application/json")
        .body("{ \"sku\": \"BOOK-1\", \"quantity\": 2 }")
    .when()
        .post("/orders")
    .then()
        .statusCode(402);
  }
}
```

## Steps

1. Confirm the acceptance module or source set (independent module for a small app; a dedicated
   source set per bounded-context module for a large one) and required dependencies exist; set
   them up first if not.
2. Choose one **high-value user scenario** and, from it, enumerate the downstream services to
   mock and the database rows / files to pin.
3. Stand up the external downstream stubs (WireMock/MockServer) and **start the application as
   its own instance** configured to point at them (container/`docker-compose`/deployed env);
   have the run pass the instance's base URL to the tests. Point REST-assured at that base URL.
4. Seed the fixed dataset into the running instance's database and write a snake_case `@Test`
   per scenario: given the seeded state and stubbed downstreams, when you drive the instance
   over the wire with REST-assured, then assert the user-visible outcome with AssertJ (and
   verify the downstream interaction against the stub where it is part of the contract).
5. Run the acceptance task (e.g. `gradle acceptanceTest` / `mvn verify`) to confirm the tests
   pass, and summarise the user scenarios covered for the user.
