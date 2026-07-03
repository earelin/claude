---
name: java-unit-test
description: Write JUnit unit tests for Java code using AssertJ assertions and Mockito for test doubles, with snake_case test method names and a preference for stubs over mocks. Use when the user wants to write, add, or scaffold Java unit tests, or asks how to test a Java class or method.
---

# Write a Java unit test

Write JUnit 5 unit tests for Java classes. Assert with **AssertJ**, create test doubles with
**Mockito**, name test methods in **snake_case**, and **prefer stubs over mocks**.

## Before you write

1. Read the project's existing tests and build file (`pom.xml` / `build.gradle`) — the repo's
   own conventions, JUnit version, and available libraries win over this skill.
2. Identify the unit under test and its collaborators. A collaborator you own and can control
   through return values is a candidate for a **stub**; only reach for a mock when the
   interaction itself is the behaviour being verified.
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
- **Test doubles use Mockito.** Prefer annotation-driven setup (`@ExtendWith(MockitoExtension.class)`
  with `@Mock` fields) or `mock(Type.class)`.
- **Prefer stubs over mocks.** Default to configuring return values with `when(dep.call())
  .thenReturn(...)` (or `doReturn`) and then asserting on the unit's output — state
  verification. Only use interaction verification (`verify(...)`) when the side effect on a
  collaborator *is* the contract under test (e.g. an event was published, a row was deleted)
  and there is no observable return value to assert against. Do not add `verify(...)` calls
  that merely restate a stubbing.
- **One behaviour per test.** Structure each test as Arrange / Act / Assert (Given / When /
  Then). Keep a single logical assertion focus per test; use AssertJ's soft assertions
  (`SoftAssertions` / `assertThatCode`) rather than sprawling unrelated checks.
- **Keep it a unit test.** No Spring context, database, network, or filesystem — substitute
  collaborators with stubs. Make tests deterministic (inject clocks, seeds, and IDs rather
  than reading `now()` or random inside the test).

## Format

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;   // stubbed collaborator

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
        // interaction verification is justified here: the published event is the contract,
        // there is no return value to assert on
        orderService.place(new Order(10));

        verify(eventPublisher).publish(any(OrderPlacedEvent.class));
    }
}
```

## Steps

1. Read the class under test and note each collaborator and each observable output (return
   values, thrown exceptions, and genuine side effects).
2. Enumerate behaviours to cover: the happy path, boundary and edge cases, and each error /
   exception path.
3. For each behaviour, write a snake_case `@Test`: arrange by stubbing collaborators, act on
   the unit, and assert the output with AssertJ. Reserve `verify(...)` for contracts that are
   purely a side effect.
4. Confirm the tests compile and pass (`mvn test` / `gradle test`), and summarise the
   behaviours covered for the user.
