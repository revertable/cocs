# Engineering decisions

[COC'S](../README.md) · [한국어](engineering-decisions.ko.md)

These notes connect the author's experiences in the original README and subsequent recollections on the same topics with selected implementation code. The reading order follows screen structure, HTTP requests, grid integration, and server persistence.

<a id="case-01"></a>
## 01 — Shared screen state and component initialization

**Intent.** I wanted a structure that made the entire screen cycle understandable and controllable. Drawing on Vuex patterns, I organized screen state under the `viewX` namespace and connected content changes to UI initialization. My goals were to reduce repetitive work and unnecessary DOM updates and keep individual features loosely coupled.

**Choice.** A shared screen convention brings initialization and event handlers together. SBUx integration connects content changes, rendering, grids, button actions, permissions, and routing into a common cycle. This gives the team a consistent way to build screens with frequent navigation.

**Read the code.** Start with [ViewX.js](../cases/01-screen-lifecycle/ViewX.js), then [SBUx-render.js](../cases/01-screen-lifecycle/SBUx-render.js) and [Menu.js](../cases/02-requests-and-navigation/Menu.js). Continue with the `viewX` declaration and handlers in [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html).

**Experience.** A teammate's “React has something like this” comment helped shape the conventions. The Vue-like familiarity another teammate described reflected my aim to make the shared structure recognizable. I felt this approach simplified development and maintenance and increased team productivity.

<a id="case-02"></a>
## 02 — Familiar HTTP APIs and navigation control

**Intent.** exios adopts axios-style APIs to make HTTP calls familiar and intuitive. I designed it for use in the project's legacy frontend environment, including JSP-based development. Request-token management aimed to control concurrent work and duplicate requests, and the shared navigation convention connected requests to screen transitions.

**Read the code.** Read [Korbiztech.exios.js](../cases/02-requests-and-navigation/Korbiztech.exios.js), [Menu.js](../cases/02-requests-and-navigation/Menu.js), and [Router.js](../cases/02-requests-and-navigation/Router.js) together. The files show the shared request utility, content-loading entry point, and URL-based duplicate-navigation check.

**The experience recorded in the original README.** During SPA-style screen transitions, a long-running query from a previous screen returned after navigation and its response rendered on the next screen. I recalled a timing interaction between page transitions and separate JavaScript modules: the response passed a validity decision and reached rendering while cancellation was expected to take effect.

The improvement direction I remember was to **recheck validity when the response arrived** and discard or ignore requests after the relevant point in time. This experience brought the practical importance of request timing, state preservation, and invalidation into focus.

**Lesson.** A reliable asynchronous interaction depends on controlling when state and responses remain valid. This is the lesson I recorded from that experience.

<a id="case-03"></a>
## 03 — Reusable grid operations and integration

**Intent.** I brought grid data entry, queries, and Excel integration into shared code. Page structure, permissions, and menu binding formed part of the broader UI/UX integration strategy.

**Subsequent recollection.** To meet the project's requirements, I combined SBGrid's APIs with custom JavaScript behavior and organized the result as a common extension layer. Shared configuration, API wrappers, row editing and deletion, and switching between editing and aggregate display could then be reused across business screens.

**Read the code.** [Korbiztech.js](../cases/03-grid-integration/Korbiztech.js) preserves the complete original utility file. Read the grid configuration and creation functions, then `gridAction`, and follow their use in [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html). Keeping the file together preserves its original organization and neighboring utilities.

**Experience.** Building the common foundation was a priority for the team's delivery. My development sequence established shared behavior first, then refined detailed validation and display rules. This reflects the balance I sought between structural consistency and the project's schedule.

<a id="case-04"></a>
## 04 — Connecting screen operations to server persistence

**Intent.** The common screen structure extends through grid operations to backend business logic. BGA010 offers a compact example of this connection, while the original README's BGA030 demonstrates a broader business screen with trees, multiple grids, uploads, and modals.

**Read the code.** Follow the files in this order:

| Step | Selected original files |
|---|---|
| Screen state, events, queries, and save handler | [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html) |
| Shared grid operations and request API | [Korbiztech.js](../cases/03-grid-integration/Korbiztech.js) · [exios](../cases/02-requests-and-navigation/Korbiztech.exios.js) |
| HTTP entry point and business service | [BGA010_RestController.java](../cases/04-grid-save-pipeline/BGA010_RestController.java) · [BGA010_Service.java](../cases/04-grid-save-pipeline/BGA010_Service.java) |
| Change data, parsing, and business values | [SaveGridDataVO.java](../cases/04-grid-save-pipeline/SaveGridDataVO.java) · [JsonUtil.java](../cases/04-grid-save-pipeline/JsonUtil.java) · [BGA010_VO.java](../cases/04-grid-save-pipeline/BGA010_VO.java) |
| Persistence interface and SQL mapping | [BGA010_DAO.java](../cases/04-grid-save-pipeline/BGA010_DAO.java) · [BGA010.xml](../cases/04-grid-save-pipeline/BGA010.xml) |

### Error-message policy: disagreement with the technical director

A technical director advocated detailed server responses identifying which field caused a database error. I favored immediate frontend validation and feedback where the user was working.

The policy we chose assigned real-time validation and detailed guidance to the frontend, request-validity decisions to the server, and minimal backend disclosure for security. I felt this division simplified the overall cycle and maintenance. Frontend developers described it as “unexpectedly easy and clear.”

### Service and ServiceImpl

Some developers favored service interfaces for mock implementations and future alternatives, emphasizing extensibility and flexibility. My practical experience led me to weigh those possibilities against the implementation and maintenance cost of additional layers. In many SI projects, concrete service classes covered the actual implementation needs.

We chose a single concrete service-class structure. I felt it improved development speed and readability and made maintenance simpler. My subsequent recollection places this choice in the context of the short schedule and the team's ability to deliver and maintain the system.

## Sources

- [Original README at the baseline commit](https://github.com/revertable/cocs/blob/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb/README.md): project scope, role, technology, design intent, team feedback, and the three decision anecdotes.
- The author's subsequent recollections: shared foundations and development sequence, team feedback, and SBGrid integration on those same subjects.
- [Source manifest](source-manifest.json): original paths and SHA-256 hashes for 14 complete source files selected from `ffc04bfbac31d731e98e6589f97e8ea30f4a94cb`.
