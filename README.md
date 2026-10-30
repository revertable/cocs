# COC'S

[한국어](README.ko.md)

COC'S presents selected implementation code and engineering decisions from a construction ERP project. Under a contract with Ieum I&C, I participated in building Korea Biztech's ERP system and led the development team, designing the Spring Boot architecture and implementing shared code.

The system supported user management, construction process management, proposals and approvals, and performance aggregation. The company authorized publication of this team project for a non-commercial portfolio. Publication terms are recorded in [LICENSE](LICENSE).

## My role

- Designed the backend architecture and Spring Boot module structure.
- Designed and implemented shared screen structure, permission handling, menu binding, and SBUx integration.
- Integrated SBGrid data entry, queries, and Excel workflows into shared code.
- Led technology selection and early technical reviews, established development guidelines, reviewed team members' code, and managed code quality.

SBUx and SBGrid are external products. My work focused on the shared structure and integration code around them. Combining SBGrid APIs with custom JavaScript produced a common extension layer that the team could reuse across business screens.

## Technology

| Area | Technology recorded in the original README |
|---|---|
| Backend | Java 17, Spring Boot 3.0.11 |
| Template engine | Thymeleaf 3.1.4 |
| Database | Oracle |
| Frontend component | SBGrid |
| Build tool | Gradle |

## Engineering decisions and code

The repository connects selected code with its design context. All 14 code files are **Excerpt**: complete original files selected from commit `ffc04bfbac31d731e98e6589f97e8ea30f4a94cb`, preserving their bytes, names, and comments. [The source manifest](docs/source-manifest.json) records the original paths and SHA-256 hashes.

| Case | Design focus | Starting point |
|---|---|---|
| 01 | Shared screen state and component initialization | [ViewX.js](cases/01-screen-lifecycle/ViewX.js) · [Decision](docs/engineering-decisions.md#case-01) |
| 02 | Familiar HTTP APIs and navigation control | [exios](cases/02-requests-and-navigation/Korbiztech.exios.js) · [Decision](docs/engineering-decisions.md#case-02) |
| 03 | Reusable grid operations and integration | [Korbiztech.js](cases/03-grid-integration/Korbiztech.js) · [Decision](docs/engineering-decisions.md#case-03) |
| 04 | Connecting screen operations to server persistence | [BGA010.html](cases/04-grid-save-pipeline/BGA010.html) · [Decision](docs/engineering-decisions.md#case-04) |

BGA010 provides a compact reading path through the shared save flow. The original README's representative screen, [BGA030.html](https://github.com/revertable/cocs/blob/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb/src/main/resources/templates/CM/BG/BGA030.html), combines trees, multiple grids, file uploads, and BGA031–BGA034 modals. Its [service](https://github.com/revertable/cocs/blob/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb/src/main/java/com/korbiztech/product/cocs/CM/BG/service/BGA030_Service.java) provides the broader context for grid persistence, uploads, and tree queries.

## How I approached the structure

> Structure is quality, and reducing repetition is the beginning of maintainability.

I wanted the team to understand and control the entire cycle: screen state, handlers, requests, component initialization, grid editing, and server persistence. In a short-schedule, 0 → 1 project, I made architectural decisions and built the shared foundation through hands-on development.

As development lead, I prioritized foundations and conventions for the whole team. Management assessed progress through visible business screens and asked me to implement a screen to demonstrate progress. My focus was on enabling teammates to build that screen and many others. This was a difference in how we assessed progress: individual screen delivery and the shared foundation for the team's work.

Requests concerning numeric alignment and empty-value validation arrived while basic grid operations were taking shape. I valued validation and screen polish, and chose a sequence that established shared behavior first, then refined validation and presentation rules.

A teammate's comment, “React has something like this,” helped shape the state and request conventions. A teammate experienced in React said, “Add imports and Vue-specific tags, and this is basically Vue.js.” I wanted conventions the team could recognize and use together.

ViewX plays on Vuex and represents **View + State + Context**. exios borrows its name and API style from axios. Both reflect my aim to make shared tools intuitive for teammates familiar with modern frontend development.

I described this as **implementing the philosophy of a framework**: creating reusable UX conventions through JavaScript and shared UI integration. I felt this simplified development and maintenance and improved team productivity. Later feedback stayed with me: “I've never seen someone maintain this much control and make progress on such a short schedule,” and “Building pages became really easy. Even with JSP being new to me, I could work comfortably.”

The [decision notes](docs/engineering-decisions.md) combine the original README's experiences with my subsequent recollections on the same subjects: validation responsibilities, Service/ServiceImpl, asynchronous request timing, and shared screen and grid structure.

This project is my answer to realizing a coherent structure within practical constraints. I see myself as a developer who thinks deeply about structure and adapts to schedules and requirements—a hands-on developer with a thinking mind.

## Source and publication

Code selection uses the [baseline commit](https://github.com/revertable/cocs/tree/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb). The [README at that commit](https://github.com/revertable/cocs/blob/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb/README.md) and the author's subsequent recollections provide the narrative context. The repository presents selected source files and bilingual reading notes for portfolio use.

[LICENSE](LICENSE) retains the baseline publication terms.

## Documentation history

- **2026-03-26:** Refined the portfolio README and corrected representative screen paths. Link consistency was checked in collaboration with OpenAI Codex.
- **2026-10-31:** Reorganized the portfolio into four code-reading cases, preserving original code files and aligning the English and Korean accounts of the author's experience and design choices.
