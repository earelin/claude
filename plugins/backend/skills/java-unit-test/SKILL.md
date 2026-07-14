---
name: java-unit-test
description: Use when editing Java unit tests.
---

# Write a Java unit test

Write JUnit 5 unit tests for Java classes. Assert with **AssertJ**, create test doubles with
**Mockito**, name test methods in **snake_case**, and **prefer stubs over mocks**.

## Before you write

1. Read the project's existing tests and build file (`pom.xml` / `build.gradle`) — the repo's
   own conventions, JUnit version, and available libraries win over this skill.
2. Identify the unit under test and its collaborators. A collaborator you own and can control
   through return values is a candidate for a **stub**; when the interaction itself is the
   behaviour, prefer a **recording fake** you can assert on with AssertJ over interaction
   verification.
3. Confirm AssertJ, Mockito, and JUnit are on the test classpath; if a needed dependency is
   missing, add it (or tell the user) before writing tests against it.

## Rules

- **Assertions use AssertJ.** Always `assertThat(actual)...`; never JUnit's `assertEquals`,
  `assertTrue`, or Hamcrest. Use the fluent, type-specific API — `isEqualTo`, `containsExactly`,
  `hasSize`, `isEmpty`, `extracting`, and `assertThatThrownBy` / `assertThatExceptionOfType`
  for exceptions.
- **Test method names are snake_case** and describe behaviour, not implementation — e.g.
  `returns_empty_list_when_no_orders_exist`, `throws_when_amount_is_negative`. No `test`
  prefix. Annotate with `@Test` (add `@DisplayName` only when a human-readable sentence adds
  value beyond the method name).
- **Test doubles use Mockito with strict stubbing.** Prefer annotation-driven setup
  (`@ExtendWith(MockitoExtension.class)` with `@Mock` fields) or `mock(Type.class)` — the
  extension enables Mockito's default `STRICT_STUBS`. **Never relax it:** do not use
  `lenient()` / `Mockito.lenient()`, `@MockitoSettings(strictness = Strictness.LENIENT)`, or
  `withSettings().lenient()`. An unnecessary or unused stub is a signal the test or the code is
  wrong — fix the cause, do not silence it with leniency.
- **Prefer stubs, and let strict stubbing prove interactions instead of `verify(...)`.** Default
  to configuring return values with `when(dep.call()).thenReturn(...)` (or `doReturn`) and then
  asserting on the unit's output — state verification. Because strict stubbing fails the test
  when a stubbed call never happens, a stub you assert against *already* proves the collaborator
  was called with those arguments; a `verify(...)` would only restate it, so do not add one.
  When the contract is a genuine side effect with no return value (an event published, a row
  deleted), prefer a **recording fake** — a small hand-written test double that captures the
  interaction — and assert on it with AssertJ, rather than reaching for `verify(...)`.
- **One behaviour per test.** Structure each test as Arrange / Act / Assert (Given / When /
  Then). Keep a single logical assertion focus per test; use AssertJ's soft assertions
  (`SoftAssertions` / `assertThatCode`) rather than sprawling unrelated checks.
- **Keep it a unit test.** No framework application context, database, network, or filesystem —
  substitute collaborators with stubs. Make tests deterministic (inject clocks, seeds, and IDs
  rather than reading `now()` or random inside the test).

## Format

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

  @Mock
  private OrderRepository orderRepository; // stubbed collaborator

  @InjectMocks
  private OrderService orderService;

  @Test
  void returns_total_of_all_orders_for_customer() {
    // given — stub the collaborator's return value
    when(orderRepository.findByCustomer("c-1"))
        .thenReturn(List.of(new Order(10), new Order(15)));

    // when
    int total = orderService.totalFor("c-1");

    // then — assert on the unit's output with AssertJ
    assertThat(total).isEqualTo(25);
  }

  @Test
  void throws_when_customer_id_is_blank() {
    assertThatThrownBy(() -> orderService.totalFor(""))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("customer");
  }

  @Test
  void publishes_event_when_order_is_placed() {
    // the published event is the contract and has no return value to assert on;
    // a recording fake captures it so we assert state with AssertJ instead of verify(...)
    RecordingEventPublisher events = new RecordingEventPublisher();
    OrderService service = new OrderService(orderRepository, events);

    service.place(new Order(10));

    assertThat(events.published())
        .singleElement()
        .isInstanceOf(OrderPlacedEvent.class);
  }
}
```

## Steps

1. Read the class under test and note each collaborator and each observable output (return
   values, thrown exceptions, and genuine side effects).
2. Enumerate behaviours to cover: the happy path, boundary and edge cases, and each error /
   exception path.
3. For each behaviour, write a snake_case `@Test`: arrange by stubbing collaborators (strict
   stubbing, never `lenient()`), act on the unit, and assert the output with AssertJ. Let strict
   stubbing prove interactions instead of `verify(...)`; for a pure side effect, assert on a
   recording fake.
4. Confirm the tests compile and pass (`mvn test` / `gradle test`), and summarise the
   behaviours covered for the user.
