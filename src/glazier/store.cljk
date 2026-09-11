(ns glazier.store
  "SSoT for the ISCO-08 7125 glazing job-site scheduling/logistics
  coordination actor (itonami actor pattern, ADR-2607121000 / CLAUDE.md
  Actors section; README's 'Robotics premise' — a job-site
  scheduling/logistics coordination robot performs crew scheduling,
  task/materials-usage/progress-record logging and glazing-materials
  supply-order coordination for a glazing crew under this
  advisor/governor pair, which never dispatches hardware itself, never
  performs glazing-installation work itself, and never finalizes a
  glazing-installation-execution decision or overrides a site safety
  officer's judgment — those remain the site safety officer's
  exclusive judgment). Modeled on cloud-itonami-isco-7111's
  housebuilder.store for the physical-safety-domain shape.

  Domain:

    glazier — a registered glazing crew member
              (:glazier-id, :name)
    site    — a registered glazing job site {:site-id :name
              :max-supply-cost number}. `:max-supply-cost` is an
              informational registered ceiling used only to decide
              whether a `:coordinate-supply-order` proposal escalates
              to human sign-off (the governor never blocks a
              within-threshold order outright; it only decides
              commit vs. escalate).
    record  — a committed operating record (a logged
              task/materials-usage/progress entry, a scheduled crew
              operation, a flagged safety concern, or a coordinated
              supply order) — written ONLY via commit-record!.
    ledger  — append-only audit trail, commit or hold.")

(defprotocol Store
  (glazier [s glazier-id])
  (site [s site-id])
  (records-of [s glazier-id])
  (ledger [s])
  (register-glazier! [s glazier])
  (register-site! [s site])
  (commit-record! [s record])
  (append-ledger! [s fact]))

(defrecord MemStore [a]
  Store
  (glazier [_ glazier-id] (get-in @a [:glaziers glazier-id]))
  (site [_ site-id] (get-in @a [:sites site-id]))
  (records-of [_ glazier-id] (filter #(= glazier-id (:glazier-id %)) (:records @a)))
  (ledger [_] (:ledger @a))
  (register-glazier! [s g]
    (swap! a assoc-in [:glaziers (:glazier-id g)] g) s)
  (register-site! [s st]
    (swap! a assoc-in [:sites (:site-id st)] st) s)
  (commit-record! [s record]
    (swap! a update :records (fnil conj []) record) s)
  (append-ledger! [s fact]
    (swap! a update :ledger (fnil conj []) fact) s))

(defn mem-store
  ([] (mem-store {}))
  ([seed] (->MemStore (atom (merge {:glaziers {} :sites {} :records [] :ledger []}
                                    seed)))))
