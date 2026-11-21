# Data Structures and Algorithms – Evidence Map

This is a source-based guide, not a claim that the project contains a separate textbook DSA library. The project is a web application; most data handling uses Python/Django collections and ORM querysets, plus JavaScript arrays/objects.

## Where to inspect
- `backend/swaps/views.py` – inspect skill-swap candidate retrieval, filtering and response construction.
- `backend/skills/views.py` – inspect skill listing/filtering and related queryset operations.
- `backend/learning_sessions/views.py` – inspect session filtering, state transitions and related records.
- `backend/verification/views.py` – inspect assessment question/answer processing and scoring.
- `backend/achievements/services.py` – inspect achievement evaluation across user metrics.
- `backend/wallet/services.py` – inspect transaction workflows and ordered wallet operations.
- `frontend/src/pages/LearnPage.jsx`, `SkillSwapPage.jsx`, `DashboardPage.jsx` – inspect client-side arrays, objects, filtering, mapping and rendering.

## Structures/concepts to report only when confirmed in code
- Lists/arrays and dictionaries/objects for collections and API payloads.
- QuerySets for database-backed collections and filtering.
- Sorting/ranking only where an explicit ordering or sort operation is present.
- Search/matching only where the source contains a corresponding filter or matching operation.
- Graphs, trees, linked lists, stacks, queues, heaps and hash tables should **not** be claimed as custom implementations unless a dedicated implementation is found. Database indexes or Python dictionaries do not by themselves prove a custom hash-table implementation.

## Required evidence format for the team member
For each DSA concept, record: (1) exact file and function, (2) input collection, (3) operation performed, (4) output/use in SkillSwap, and (5) time complexity only if it can be justified from the actual operation and underlying data access. Distinguish in-memory operations from database queries.
