(ns glazier.actor-test
  (:require [clojure.test :refer [deftest is testing]]
            [glazier.actor :as actor]
            [glazier.store :as store]))

(defn- fresh-store []
  (let [st (store/mem-store)]
    (store/register-glazier! st {:glazier-id "glazier-1" :name "Kobo Yamada"})
    (store/register-site! st {:site-id "S-1" :name "Kobo Tower Glazing Site" :max-supply-cost 2000})
    st))

(deftest commits-a-registered-work-log
  (let [st (fresh-store)
        graph (actor/build-graph {:store st})
        request {:glazier-id "glazier-1" :op :log-work-record :stake :low
                  :site-id "S-1" :task "glass panel install progress log"}
        result (actor/run-request! graph request {} "thread-1")]
    (is (= :done (:status result)))
    (is (some? (get-in result [:state :record])))
    (is (= 1 (count (store/records-of st "glazier-1"))))))

(deftest holds-an-unregistered-site-proposal
  (let [st (fresh-store)
        graph (actor/build-graph {:store st})
        request {:glazier-id "glazier-1" :op :log-work-record :stake :low
                  :site-id "S-ghost" :task "glass panel install progress log"}
        result (actor/run-request! graph request {} "thread-2")]
    (is (= :hold (:disposition (:state result))))
    (is (empty? (store/records-of st "glazier-1")))))

(deftest interrupts-then-approves-safety-concern-on-human-approval
  (let [st (fresh-store)
        graph (actor/build-graph {:store st})
        request {:glazier-id "glazier-1" :op :flag-safety-concern :stake :low
                  :site-id "S-1" :hazard-type :glass-handling-risk}
        interrupted (actor/run-request! graph request {} "thread-3")]
    (is (= :interrupted (:status interrupted)))
    (is (empty? (store/records-of st "glazier-1")))
    (let [resumed (actor/approve! graph "thread-3")]
      (is (= :done (:status resumed)))
      (is (= 1 (count (store/records-of st "glazier-1")))))))

(deftest holds-a-scope-excluded-op-even-at-high-confidence
  (testing "an actor run can never commit a proposal that would finalize a glazing-installation-execution decision, regardless of disposition path"
    (let [st (fresh-store)
          graph (actor/build-graph {:store st})
          request {:glazier-id "glazier-1" :op :finalize-glazing-installation-decision :stake :low
                    :site-id "S-1" :task "glazing installation decision"}
          result (actor/run-request! graph request {} "thread-4")]
      (is (= :done (:status result)))
      (is (= :hold (:disposition (:state result))))
      (is (empty? (store/records-of st "glazier-1"))))))
