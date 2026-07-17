# cloud-itonami-isco-7125

Open Occupation Blueprint for **ISCO-08 7125**: Glaziers.

This repository designs a forkable OSS business for a glazing job-site scheduling and logistics coordination practice: a job-site scheduling and supply-coordination robot manages crew/task records under a governor-gated actor, so a glazing crew keeps its own operating records instead of renting a closed workforce-management SaaS.

**Maturity: `:implemented`.** `src/glazier/` implements the
`GlazierActor` as a `langgraph.graph/state-graph`
(`glazier.actor`) wired to a `Glazier Advisor`
(`glazier.advisor`) and an independent `GlazierGovernor`
(`glazier.governor`), following the itonami actor pattern
(ADR-2607121000): `:intake -> :advise -> :govern -> :decide -+-> :commit
(:ok?) +-> :request-approval (:escalate?, human-in-the-loop interrupt)
+-> :hold (:hard?)`. HARD invariants (always hold, never
overridable): glazier provenance, site provenance, no-actuation
(`:effect` must be `:propose`), a closed op-allowlist
(`:log-work-record`, `:schedule-crew-operation`,
`:flag-safety-concern`, `:coordinate-supply-order` — nothing else may
ever be proposed), and a permanent, unconditional block on any
proposal that would directly finalize a
glazing-installation-execution decision (e.g. deciding to proceed
with a specific glass-panel installation) or override a site safety
officer's judgment. Always-escalate paths (human sign-off regardless
of confidence, mapping this repo's Trust Controls in
[`docs/business-model.md`](docs/business-model.md)):
`:flag-safety-concern` (always) and `:coordinate-supply-order` above
the registered cost threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot performs
the physical domain work**. Here a job-site scheduling/logistics coordination robot performs crew scheduling, task/materials-usage/progress-record logging and glazing-materials supply-order coordination for a glazing crew, under an actor that proposes actions and an independent **Glazier Governor** that gates them. The governor never
dispatches hardware itself, never performs glazing-installation work on the job site, and never finalizes a glazing-installation-execution decision or overrides a site safety officer's judgment; `:high`/`:safety-critical` actions (such as a flagged glass-handling/height-work/panel-hazard concern, or an above-threshold supply order) require human sign-off. **This actor coordinates job-site scheduling/logistics only — it never performs glazing-installation work itself.**

## Core Contract

```text
crew roster + job-site registration + safety-reporting policy
        |
        v
Glazier Advisor -> Glazier Governor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses, finalize
a glazing-installation-execution decision, override a site safety officer's
judgment, suppress an operating record, or disclose sensitive data
without governor approval and audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `7125`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
