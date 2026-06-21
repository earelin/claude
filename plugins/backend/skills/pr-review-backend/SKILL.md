---
name: pr-review-backend
description: Review backend pull requests and provide feedback on code changes. Use when the user asks for a review of a pull request or wants to understand the changes in a specific commit.
---

# Backend Pull Request Review skill

Analyze and provide feedback on pull requests and code changes.

## Steps

### Code Structure Checklist

1. Does the actual implementation reflect the architecture?
2. Is the code easy to understand?
3. Is the code too long?
4. Is cohesion in place?
5. Is the code modular?
6. Are components cohesive?
7. Is the code loosely coupled?
8. Is the code reusable?
9. Is the code readable?
10. Is the code easy to maintain and test?
11. Are premature optimizations in place?
12. Is composition preferred?
13. Is inheritance properly used?
14. Is the flow easy to understand?
15. Are interactions between different components easy to catch?
16. Are conditional flows completely defined?
17. Is there any undefined behavior?
18. Are APIs consistent and as clean as the overall code?

### Data Structures Checklist

1. Are data structures appropriately used?
2. Is the data structure appropriate based on the data size the code is dealing with?
3. Are potential changes to data size considered and handled?
4. Is the data structured forced to do operations not natively supported?
5. Does the data structure support growth (i.e., scalability)?
6. Does the data structure reflect the need for relationships between elements?
7. Does the data structure optimally support the operations you need to perform on it?
8. Is the choice of a specific data structure overcomplicating the code?
9. Is the data structure chosen based on most frequent operations to be performed on data?

### Design Smells Checklist

1. Are cyclic dependencies present in the code?
2. Is the code feature dense? More than one feature per component is enough to have this smell.
3. Are the dependencies stable?
4. Does the code have mashed components?
5. Are APIs clearly defined?
6. Are mesh components present?
7. Are first lady components present?
8. Are bossy components present?
9. Does the class diagram behave like a DAG?
10. Does each and every class, method, or function have a single logical responsibility to be achieved?
11. Does the class diagram show highly coupled components?
12. Is there any function, method, or class with a big LOC number?

### Software Architecture

1. Are you using design patterns properly?
2. Are the patterns the optimal choice based on requirements?
3. Are performances taken into account when choosing the design pattern you are inspecting?
4. Is the decorator a first lady?
5. Does the pattern hinder performance requirements?
6. Is your singleton behaving like a first lady?

### Naming and Formatting Conventions

1. Are redundant parameters present in the class (might be the case of instance var instead)?
2. Does the code follow the naming conventions of the chosen language (e.g., CamelCase for Java)?
3. Are names of variable, methods, and classes meaningful?
4. Are names of variable, methods, and classes descriptive?
5. Are names consistent across the codebase?
6. Are names context oriented?
7. Are keywords used for variable naming?
8. Can you understand from the function/method/class/variable name what is it expected to perform?
9. Are too many parameters in place?
10. Are optional parameters adequately used?
11. Are modifiers used appropriately?
12. Are global variables in place? Are they actually needed?
13. Does the code contain magic numbers?
14. Is abstraction by parameterization achieved?
15. Is parameterization needed to remove redundancies?
16. Are generic types used when needed to improve reusability (Java)?
17. Is naming giving insights of a bossy component?
18. Are private methods called from the outside (Python)?
19. Are spacing conventions respected?

### Comments

1. Are comments coherent with the function/method/class they describe?
2. Are comments complete?
3. Are pre- and post-conditions properly described?
4. Are exceptions and errors documented?
5. Are input and output clearly defined and documented?
6. Is it clear (or otherwise commented) the type(s) of the input(s)?
7. Is it clear (or otherwise commented) the type(s) of the outputs?
8. Are all the flows of a method described (including errors/exceptions)?
9. Are TODOs, FIX-ME, and similar comments still present in released code?
10. Too many inline comments, are they needed?
11. Are comments easy to maintain over time?
12. Are coding conventions enforced?
13. Are obvious comments avoided?
14. Are comments and documentation well maintained?
15. Are comments used to describe current code only?
16. Do comments and documentation contain typos?
17. Is the commenting style in line with language guidelines (e.g., PEP8 for Python)?

### Concurrency, Parallelism, and Performances

1. Is the code thread safe?
2. Are immutable object actually immutable?
3. Are race conditions present?
4. Is atomicity ensured?
5. Are safety and liveness ensured?
6. Are deadlocks avoided?
7. Is starvation avoided?
8. Is fairness ensured?
9. Are locking mechanisms properly used?
10. Is consistency ensured? 11. Is isolation ensured? 12. Is durability guaranteed? 13. Are you trying to parallelize a problem which is notoriously known as hard to parallelize? 14. Are you considering task and data granularity when parallelizing code? 15. Are you considering and properly embracing locality needs in your parallelized solution? 16. Is load balancing appropriately used? Is the workload uniformly split? 17. Are you considering the inherent costs of parallelizing a solution into the overall performances? 18. Do you have proper prioritization of performance metrics that are specific to the context your parallel application runs in?