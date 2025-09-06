# GSoC '25: Create a service to get a new project to Fedora more easily

<img width="250" height="251" alt="image" src="https://github.com/user-attachments/assets/6bacd466-eaab-434b-b947-7c80d588aee0" />

## Contributor Information

-   **Name:** Mayank Singh
-   **Email:** mr.mayankgame@gmail.com
-   **GitHub:** https://github.com/mynk8
-   **Organization:** [Fedora Linux](https://www.fedoraproject.org/)
-   **Project Repository:** https://github.com/packit/avant
-   **Mentors:** František Lachman
-   **Project Duration:** 350 hours

---

## 1. Description

The project introduces a proof-of-concept Git-based workflow for the Fedora package review process, an alternative to the older Bugzilla-based system.

Contributors can now create, submit, and manage their package reviews through Pull Requests in a centralized Forgejo repository. The service takes care of building the packages, testing, and compliance checking with the `fedora-review` tool, and provides real-time feedback on the PR page.

By integrating Git with automated tooling in a centralized place, it's much easier for new contributors to get their packages into the distribution.

---

## 2. Background & Motivation

The traditional workflow for submitting a new package to Fedora was fragmented and tedious, relying on a manual process involving `.spec` files, external file hosting, and Bugzilla tickets. The old process required manual Bugzilla tickets and external file hosting, which had slower feedback loops, often discouraging potential contributors.

This project was motivated by the critical need to modernize this experience. By creating a centralized, pull-request based workflow, the goal was to lower the barrier to entry for people with less experience in packaging and improve the overall efficiency of the Fedora package ecosystem.

---

## 3. Goals & Objectives

The key objectives were:
Of course. Here is a revised version of the "Goals & Objectives" section, structured to explicitly align the project's outcomes with the official GSoC deliverables for clear comparability.

* **Demonstration/Prototype:** A fully functional proof-of-concept service was developed. The entire user journey, from local commit to receiving automated feedback, is operational and detailed in the **Walkthrough** section of this report.

* **Source Code Repository:** The project's source code can be found at [avant](https://github.com/packit/avant) and certain parts were contributed to established, public Fedora tooling. This includes feature additions to the `packit-service` repository and its `ogr` dependency to implement the Forgejo integration.

* **Integration of Automatic Feedback:** On every new commit to a PR, the service automatically triggers **COPR builds**, runs **installation tests via Testing Farm**, and performs **compliance checks with `fedora-review`**, posting all results directly to the PR for immediate review.

* **Deployment:** The service is **containerized**, ready for deployment. The final step, which is to have the service run publicly on Communishift, ticket was opened here [fedora-infra ticket #12758](https://pagure.io/fedora-infrastructure/issue/12758).

* **Testing and Documentation** The instructions for setting up the service secrets is similar to packit-service, a comprehensive user walkthrough and usage guide is to be included. [validation](https://github.com/packit/validation) tests are used for checking if the COPR and Testing Farm jobs are running as expected.

---

## 4. Walkthrough

A simple walkthrough of the service.

### 1. Prepare the Package

The journey begins in a familiar Git environment. A contributor starts by forking the central review repository to create their own personal workspace. In their local clone, they add the essential packaging files: the `.spec` file that defines how the package is built and the `sources` file containing the upstream code.

<img width="1313" height="378" alt="Adding package files locally" src="https://github.com/user-attachments/assets/fc783da6-0787-4ae1-9c23-1828924a46d0" />

<img width="1313" height="573" alt="Local git commit" src="https://github.com/user-attachments/assets/ebaef85c-d9a3-4670-9f71-813f0e983e25" />

### 2. Submit for Review

Once the files are ready, the contributor commits their changes and opens a Pull Request against the main repository. This single action is the trigger that brings the entire automated review system to life. No more Bugzilla tickets or manual uploads—the PR is now the central hub for everything that follows.

<img width="1861" height="913" alt="Creating a Pull Request" src="https://github.com/user-attachments/assets/2bd21ee5-ee13-4749-be75-4101df7afdc3" />

### 3. Receive Instant, Automated Feedback

Immediately after the PR is opened, the service gets to work. The contributor can see real-time status checks appear directly on the PR page, starting with the SRPM build.

<img width="975" height="421" alt="Initial status checks on the PR" src="https://github.com/user-attachments/assets/746ba893-6a07-4698-a186-32904072f62e" />

When the checks are completed, the service posts a comment with the results of the `fedora-review` tool. This provides immediate, actionable feedback, clearly categorizing issues into "must," "should," and "extras," so the contributor knows exactly what to address.

<img width="1861" height="803" alt="Fedora-review results posted as a comment" src="https://github.com/user-attachments/assets/5759ec64-3811-4bcd-a5d4-d266efde088b" />

One can inspect the list of checks that failed, are pending, and are also categorized by "should," "must," and "extras." After a while, `rpmlint` and install checks are finished; they are failing in this case.

<img width="1561" height="421" alt="Failed rpmlint and install checks" src="https://github.com/user-attachments/assets/e0db75de-4abc-45c6-81b4-2e8b3ce7e0c2" />

We can inspect the logs by clicking the "Details" link, which redirects the user to Packit's dashboard.

<img width="1562" height="421" alt="Packit service dashboard" src="https://github.com/user-attachments/assets/9dc2bd57-41ee-4773-b7fc-0518ad7a12ed" />

<img width="1562" height="509" alt="Build details in the dashboard" src="https://github.com/user-attachments/assets/d1948864-7562-48f7-ac23-77a51732a9d6" />

Checking the latest entry here, we can find the Testing Farm run and inspect the logs to trace the cause of failure. 
We can see that the checks failed due to a broken package and missing dependencies.

<img width="1562" height="454" alt="Testing Farm logs showing failure" src="https://github.com/user-attachments/assets/e1990ba0-4e2b-4f67-93e5-89ff8539edaf" />

The reported problems can then be fixed locally, push the changes, the checks run again to provide feedback again.

### 4. Iterate and Collaborate

With clear logs and feedback, the contributor can fix the issue in their local repository and simply push a new commit. The service automatically detects the change and re-runs the entire suite of checks, providing updated feedback.

This creates a tight, rapid feedback loop. Experienced packagers can also join the discussion directly in the PR, reviewing the code and inspecting the same test results, making collaboration seamless and efficient.

---

## 5. Project Timeline

| Dates                  | Milestones & Goals                                                                                                                                                                                             |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [June 27 - July 7](https://communityblog.fedoraproject.org/simplifying-fedora-package-submission-progress-27-june-14-july-gsoc-25/)     | **Foundation & Architecture**<br>• Present high-level architecture to Fedora community<br>• Decide on monorepo model for package submissions<br>• Set up local development environment with `packit-service`<br>• Begin prototyping user flow with new APIs and handlers |
| [July 8 - July 15](https://communityblog.fedoraproject.org/simplifying-package-submission-progress-8-july-15-july-gsoc-25/)     | **Forgejo Integration & Event Handling**<br>• Migrate service forge to Forgejo (open-source alternative to GitHub)<br>• Add support for parsing issue and push events<br>• Implement parsing commands from issue comments<br>• Resolve technical challenges with package source handling |
| [July 15 - July 22](https://communityblog.fedoraproject.org/simplifying-package-submission-progress-15-july-22-july-gsoc-25/)    | **Core Functionality & Build System**<br>• Extend Forgejo support in `packit-service`<br>• Write sample handler for reading from new Pull Requests<br>• Enable dynamic package config construction for builds<br>• Get COPR integration running for package builds |
| [July 22 - July 29](https://communityblog.fedoraproject.org/simplifying-package-submission-progress-22-july-29-july-gsoc-25/)    | **Status Reporting & Integration**<br>• Implement commit status reporting for package actions<br>• Add support for Forgejo commit status API in `ogr` library<br>• Enable `fedora-review` tool for all COPR builds<br>• Begin work on status reporting and comments |
| [July 29 - August 7](https://communityblog.fedoraproject.org/?p=15016&preview=1&_ppp=af8f79d7c0)   | **Enhanced Status & Feedback**<br>• Complete commit status implementation for Forgejo<br>• Report build and test results directly to PRs<br>• Focus on comment functionality and status updates<br>• Begin unit testing for new functionality |
| [August 7 - August 14](https://communityblog.fedoraproject.org/?p=15017&preview=1&_ppp=f76ab3be62) | **Workflow Optimization**<br>• Eliminate requirement for `packit.yaml` configuration<br>• Align with standard `dist-git` layout<br>• Parse specfile directly from PRs<br>• Resolve container and dependency issues |
| [Aug 14 - Aug 22](https://communityblog.fedoraproject.org/?p=15019&preview=1&_ppp=07904fa547)      | **Testing & Reporting Improvements**<br>• Implement separate status reporting for `rpmlint` and installation tests<br>• Switch to JSON-based `fedora-review` reports<br>• Improve comment formatting with collapsible sections<br>• Work on unit testing challenges |
| [Aug 22 - Aug 29](https://communityblog.fedoraproject.org/?p=15007&preview=1&_ppp=833ee35c05)      | **Final Integration & Documentation**<br>• Complete Testing Farm integration<br>• Finalize automated review workflow<br>• Prepare project demonstration                                                              |
| `August 29+`           | **Deployment & Upstream Contribution**<br>• **[Pending]** Deploy service containers for public use on Communishift [fedora-infra ticket](https://pagure.io/fedora-infrastructure/issue/12758) <br>• Configure [validation](https://github.com/packit/validation/tree/main) tests <br>• Write documentation<br>• **[Pending]** Merge Forgejo integration code into upstream. [#2835](https://github.com/packit/packit-service/pull/2835) and [#896](https://github.com/packit/ogr/pull/896) |

---

## Learnings and Acknowledgements

### Key Learnings

-   **Git Forge Integration:** Gained a deep understanding of interfacing with Git forges by contributing Forgejo support to the `ogr` library.
-   **Container Orchestration:** Developed skills in managing containerized services, secrets, and resolving deployment challenges on Communishift (OpenShift/Kubernetes).
-   **Asynchronous Service Debugging:** I got much more comfortable debugging async services, especially when failures couldn’t be reproduced locally.
-   **API and Webhook Handling:** Learned best practices for parsing, digesting, and securely handling webhook data from external services.

### Future Plans

This service provides a strong foundation for further innovation. Future enhancements could include:

-   **Automated `dist-git` Onboarding:** Extending the service to automatically create a new `dist-git` repository and push the approved package upon PR merge.
-   **Monorepo Dependency Handling:** Supporting complex scenarios where a submitted package has dependencies that also exist within the same PR or needs review.

### Acknowledgements

-   **František Lachman:** Grateful for the constant support and guidance throughout the project.
-   **Fedora Community:** For their support, discussions, and feedback that helped shape the project.

Overall, GSoC was a great learning experience for me.
