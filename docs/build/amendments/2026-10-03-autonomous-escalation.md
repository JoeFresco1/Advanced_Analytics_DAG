# Autonomous build escalation

The user explicitly requested autonomous escalation to stronger models after repeated build review failures. This amendment changes build control, not analytical rerun rules or accepted product contracts.

Worker lanes: GPT-6 Luna xhigh, GPT-6 Sol xhigh after one rejected work attempt, GPT-6 Astra xhigh after two. A separate GPT-6 Sol process reviews the deliverable. The event journal is authoritative; retries and cycle resets cannot erase rejection history. Infrastructure failures do not advance the model lane. Live probes confirmed Astra and Sol through this account; gpt-6.1-sol was rejected as unavailable and is not configured.

Pending units receive three bounded semantic work/review passes, plus bounded infrastructure retries. The already-twice-rejected registry receives the Astra repair lane now, with its saved artifacts and exact rejection. Review, frozen-contract, file-ownership and dependency gates remain mandatory. An exhausted strongest lane remains a real reported blocker rather than automatic approval.

The independent supervisor automatically recovers an exited runner with interrupted tasks, resumes from saved work and reaps that run's orphans through the supported CLI. It allows two crash restarts within the existing wall-time budget. It never blindly requeues a final substantive review rejection.

Tests cover lane selection, deduplication, reset persistence, unrelated-task isolation, infrastructure distinction, actual crash/resume behavior, and final semantic-rejection stopping. Evaluation-model baselines remain pinned independently of implementation escalation.
