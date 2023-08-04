## Fuzzing Checklist

The following checklist is intended for authors and reviewers of fuzzing papers. It lists a number of recommendations for a fair and reproducibile evaluation that should be followed in general, even though we stress that individual points may not apply in specific scenarios, requiring human consideration on a case-by-case basis. This checklist is not exhaustive but aims to point out common pitfalls with the goal of avoiding these.

More details regarding each category of checkmarks are available in Section 5 of our [paper](http://filled-in-after-paper-is-published)

- Artifact
  - Documentation
    - [ ] Is the process of building/preparing the artifact documented?
    - [ ] Is the interface used for interaction with the fuzzer documented?
    - [ ] If the approach extends another fuzzer, are the interfaces (i.e., hooked functions, added compiler passes) documented?
    - [ ] Are the individual experiments documented and how to compare their results against the paper?
    - [ ] If the new approach is based on another fuzzer, is the version of the baseline documented?
    - [ ] Are the versions of the targets and other fuzzers used during evaluation specified?
  - Completeness
    - [ ] Are the other fuzzers, or instructions on setting them up, provided?
    - [ ] Are all experiments required to back the paper's main claims included?
  - Reusability
    - [ ] Can the fuzzer be executed independently of the underlying system, e.g., through virtualization or container engines?
    - [ ] Are there external dependencies (e.g., tarballs downloaded via https), that may be unavailable in the future?
    - [ ] Is the commit history of projects the fuzzer is based on available and not squashed?

- Targets used for the evaluation
    - [ ] Are the targets suitable to show the strengths of the approach? For example, are parsers targeted if the approach is related to grammar fuzzing?
    - [ ] Is it documented how the targets need to be prepared for fuzzing?
    - [ ] Are all modifications (e.g., patches, changes to the runtime environment) applied to targets documented?
    - [ ] Are targets used that are also tested by related work (to allow comparability)?
    - [ ] If applicable for the approach, are benchmarks such as FuzzBench used?
    - [ ] If using benchmarks, are benchmarks injecting artificial bugs avoided?

- Competitors used for comparison
    - [ ] Is the approach compared against state-of-the-art tools in the respective field?
    - [ ] Is the baseline, i.e., the fuzzer the new approach is based on (if any), compared against?
    - [ ] If some of the state-of-the-art fuzzers failed on some targets, are the reasons sufficiently documented?

- Setup used for evaluation
    - [ ] Is the used hardware (e.g., CPU, RAM) documented?
    - [ ] Is a sufficiently long (>=24h) runtime used for comparison?
    - [ ] Is the number of repetitions documented and sufficiently large (>= 10)?
    - [ ] Had all fuzzers access to the same amount of computation time? This requires particular thought if a tool requires precompuation(s).
    - [ ] Is the runtime sufficient such that all fuzzers are flatlining towards the end of the fuzzing runs?
    - [ ] Is it documented how many instances of each fuzzer have been run in parallel?
    - [ ] Is it documented how many CPUs have been available to each fuzzing process (e.g., via CPU pinning)?
    - [ ] Is the setup (e.g., available cores, hardware) for fuzzers compared to in the evaluation suitable according to their requirements?
    - [ ] Seeds:
      - [ ] Are the used seeds documented?
      - [ ] Are the used seeds uninformed?
      - [ ] Are the seeds publicly available?
      - [ ] Were all fuzzers provided with the same set of seeds?
      - [ ] If informed seeds are used, is the initial coverage achieved by those visible from, e.g., plots?

- Evaluation Metrics
    - [ ] Are standard metrics, such as coverage over time or the number of (deduplicated) bugs, used?
    - [ ] Is it specified how coverage was collected?
    - [ ] Is a collision-free encoding used for coverage collection?
    - [ ] If collecting coverage for JIT-based emulation targets, are basic blocks reported instead of translation blocks (jitted blocks)?
    - [ ] Is no metric used the new approach is naturally optimized for?
    - [ ] Bug finding:
      - [ ] Are targets used that are relevant (have a sufficient user base, forks, GitHub stars, ...)?
      - [ ] Are the targets not deprecated and still receive updates?
      - [ ] Are the targets known for having unfixed bugs?
      - [ ] If reproducing existing CVEs, are the CVEs themselves valid and not duplicates or rejected?
      - [ ] Is an uninstrumented binary used for crash deduplication and reproduction?
      - [ ] Is the process of deduplication and triaging crashes described?
      - [ ] Are CVEs IDs provided (if possible anonymously)?
      - [ ] Are the CVEs referencing different bugs and not duplicates?

- Statistical Evaluation
  - [ ] Are measures of uncertainty, such as intervals in plots, used?
  - [ ] Is a sufficient number of trials used (>= 10)?
  - [ ] Are reasonable statistical tests used to check for the significance of the results?
  - [ ] Are reasonable statistical tests used to study the effect size?

- Other
  - [ ] Are threats to validity considered and discussed?

