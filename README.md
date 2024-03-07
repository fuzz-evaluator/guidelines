# Fuzzing Evaluation Guidelines


Current version: 1.0.2

Proposals for changes welcome (please open an issue for discussion or a pull request for changes).

DISCLAIMER: These items represent are a best-effort attempt at capturing action items to follow during the evaluation of a scientific paper that focuses on fuzzing. **They do not apply universally to all fuzzing methods - in certain scenarios, techniques may wish to deviate for good reason from these guidelines. In any case, a case-by-case judgment is necessary.**
The guidelines do not discuss many malicious choices that immediately negate any chance of a fair evaluation, such as giving your fuzzer an unfair advantage (e.g., by fine-tuning the fuzzer or its targets) or putting other fuzzers at a disadvantage.



A. Preparation for Evaluation
  1. Find relevant tools and baselines to compare against
    - 1.1 Include state-of-the-art techniques from both academia and industry
    - 1.2 If your fuzzer is based on an existing fuzzer, include the baseline (to measure the delta of your changes, which allows attributing improvements to your technique)
    - 1.3 Use recent versions of fuzzers
    - 1.4 If applicable, derive a baseline variant of your technique that replaces core contributions by alternatives. For example, consider using a variant that replaces an informed algorithm with randomness.
    - 1.5 If using AFL-style fuzzers, do not use afl-gcc but afl-clang-fast or afl-clang-lto.

  2. Identify suitable targets for the evaluation
    - 2.1 If applicable, consider using evaluation benchmarks, such as Fuzzbench (this allows to test many fuzzers under standardized conditions)
    - 2.2 Select a representative set of programs from the target domain
    - 2.3 Include targets used by related work (for comparability reasons)
    - 2.4 Do not cherry-pick targets based on preliminary results
    - 2.5 Do not pick multiple targets that share a considerable amount of code (e.g., two wrappers for the same library)
    - 2.6 Do not use artificial programs or programs with artificially injected bugs

  3. Derive suitable experiments to evaluate your approach
    - 3.1 Evaluate on found bugs (if applicable)
      - 3.1.1 If using *new* bugs,
        - 3.1.1.1 include whether other fuzzers find the bug as well (so you can attribute finding this bug to your technique rather than being the first to fuzz this target); other fuzzers must have had the same computing resources
        - 3.1.1.2 deduplicate crashing inputs to derive the true bug count
          - 3.1.1.2.1 If possible, use vendor confirmation to identify true bugs
          - 3.1.1.2.2 Otherwise use manual triaging (consider automated deduplication as a pre-step to reduce number of findings)
        - 3.1.1.3 do not fuzz unsuitable programs for the sake of finding bugs (e.g., small hobby projects that are no longer maintained are not suitable)
        - 3.1.1.4 do not search for bugs in unstable, fast moving development branches, but prefer stable/release versions
      - 3.1.2 If using *known* bugs,
        - 3.1.2.1 use the known bugs as ground truth
        - 3.1.2.2 take into account that known bugs may not have been deduplicated
        - 3.1.2.3 do not evaluate on artificial bugs
      - 3.1.3 Do not use the number of (unique) crashing inputs as bug count

    - 3.2 Evaluate code coverage over time (if applicable)
      - 3.2.1 If possible, use source code-based coverage (e.g., llvm-cov or lcov)
      - 3.2.2 Otherwise use a collision-free encoding
      - 3.2.3 Measure coverage on a neutral binary; this binary should include only instrumentation needed to measure the coverage, but not sanitizers or fuzzer-specific instrumentation
      - 3.2.4 If using dynamic binary translation, the coverage measurement should be independent of the translation (e.g., emulators may split a basic block into multiple translation blocks, disturbing measurements)

    - 3.3 If applicable, evaluate domain-specific aspects of your fuzzer

    - 3.4 If applicable, conduct ablation studies to measure individual design choices

    - 3.5 If applicable, evaluate the influence of hyperparameters on your design

    - 3.6 If doing experiments using custom metrics,
      - 3.6.1 take special care to ensure a fair comparison to existing work.
      - 3.6.2 In particular, avoid queue survivor bias (i.e., the queue only contains input fulfilling specific criteria) as it may favor your fuzzer. For example, your fuzzer optimizing towards the new, custom metric may keep inputs in the queue that others discard (even though they find the input at runtime) -- evaluating only inputs on the queue thus gives your fuzzer an unfair advantage.


B. Documenting the Evaluation
  1. Describe the setup, including
    - 1.1 hardware used (such as CPU and RAM)
    - 1.2 how many cores have been available to each fuzzing campaign (e.g., via CPU pinning)
    - 1.3 technologies used, such as Docker or virtualization
  2. Choose and document experiment parameters, including
    - 2.1 a sufficiently long runtime (if possible >= 24h)
    - 2.2 a sufficient number of repetitions/trials to account for randomness and enable a robust statistical evaluation (if possible >= 10 trials)
    - 2.3 fairness of computing resource allocation, i.e., all fuzzers have access to the same amount of computation resources. This requires particular consideration if a tool requires pre-computation(s).
    - 2.4 suitable seeds:
      - 2.4.1 If possible, use uninformed seeds for coverage evaluation (for bug experiments, informed seeds may be beneficial)
      - 2.4.2 Otherwise identify the coverage achieved by the initial seed set
      - 2.4.3 Provide all fuzzers with the same set of seeds
      - 2.4.4 Publish the used set of seeds
    - 2.5 targets:
      - 2.5.1 Use recent versions
      - 2.5.2 If applicable, explain modifications to the programs or runtime environment (e.g., when you patch the program or set a lower stack size)
    - 2.6 other tools/fuzzers:
      - 2.6.1 Use recent versions
      - 2.6.2 If your fuzzer is based on another one, make sure the version you base your tool on and the one used in the evaluation are the same


C. Experiment Postprocessing
  1. Data Analysis
    - 1.1 Run a robust statistical evaluation to measure significance, such as Mann-Whitney U or bootstrap-based methods
    - 1.2 Measure effect size using a test such as the Vargha and Delaney A^\_{12} test
  2. Data Visualization
    - 2.1 If applicable, plot absolute values (such as for coverage over time)
    - 2.2 Measure uncertainty, for example using standard deviation or (confidence) intervals in plots
  3. Bug Handling
    - 3.1 Deduplicate and triage crashing inputs
    - 3.2 Report new bugs
      - 3.2.1 Follow responsible disclosure guidelines
      - 3.2.2 If possible, minimize samples before reporting
      - 3.2.3 If possible, attach available information, such as precise environment (OS, compilation flags, command line arguments, ...), ASAN reports, and (minimized) crashing input
      - 3.2.4 Consider reporting the bug with an anonymous identity and link to it in the paper during submission, such that reviewers can assess the bug and its impact themselves
    - 3.3 CVEs
      - 3.3.1 CVEs should be requested by maintainers
      - 3.3.2 If the maintainers do not request a CVE, link to the bug tracker instead of requesting a CVE yourself


D. Artifact Release
  1. Artifact Contents
    - 1.1 Publish your code on a platform ensuring long-term availability, such as Zenodo or GitHub
    - 1.2 Publish modifications of other tools
      - 1.2.1 If modifying other tools, publish any modifications
      - 1.2.2 Publish your integration of other tools
    - 1.3 If possible, publish experiment data
  2. Artifact Documentation
    - 2.1 Document how to build your fuzzer
    - 2.2 Document how to interact with your fuzzer
    - 2.3 Document the source code
    - 2.4 Document modifications/extensions to other tools and their integration
    - 2.5 Document how to run and reproduce experiments described in the paper
  3. Artifact Reusability
    - 3.1 Specify versions of all tools used
    - 3.2 If possible, enable execution of your fuzzer independent of the underlying system, e.g., through virtualization or container engines
    - 3.3 Avoid external dependencies that may be unavailable in the future, such as tarball downloads via https
    - 3.4 Pin versions of dependencies
    - 3.5 If applicable, maintain the commit history of underlying tools instead of squashing them
    - 3.6 Double-check your code is complete and reusable
