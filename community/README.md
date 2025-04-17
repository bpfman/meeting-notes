# bpfman Community Meeting Notes 2025

This document contains the notes of the bpfman community meeting.

This document is best viewed and edited online: [![hackmd-github-sync-badge](https://hackmd.io/jZ8odWiqRGCQYqumtExJCQ/badge)](https://hackmd.io/jZ8odWiqRGCQYqumtExJCQ)

[TOC]

## April 24th 2025 10:00 AM EDT/EST

:::info

- **Location:** https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e
- **Date:** April 24th 2025 10:00 AM EDT/EST
- **Host:** @dave-tucker
- **Participants:** 
:::

- Issue Triage


## April 17th 2025 10:00 AM EDT/EST

:::info

- **Location:** https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e
- **Date:** April 17th 2025 10:00 AM EDT/EST
- **Host:** @dave-tucker
- **Participants:** Billy McFall, Andrew McDermott, Dave Tucker, Andre Fredette
:::

Agenda:
  - Issue Triage
  - [Billy] SPO:
    - [operator security-profiles-operator (0.9.1)](https://github.com/k8s-operatorhub/community-operators/pull/5983) merged in the community OperatorHub. Still need pushed to the OCP OperatorHub. Still leaning on the maintainer.
    - SPO had a CRD change and was failing `orange` tests, which are Operator upgrades. bpfman will have the same issue. To get passed it, SPO had to release the new version in a new channel. See the above PR when comes time for bpfman to push to OperatorHub for v0.6.0. See also [operator [CI] sonataflow-operator (10.0.0)](https://github.com/k8s-operatorhub/community-operators/pull/5428).
    - Andy, do you know how to release in RedHat OperatorHub? Upstream release notes don't cover it. I think it's in the "catalog" directory in [OCP](https://github.com/openshift/bpfman-operator/tree/main/catalog).
  - [Dave] Using Scarf (https://about.scarf.sh/)
      - Use vanity URLs for image and binary downloads
      - Tracks active users and adopters
      - Dave to set this up as no objections
  - [Andre] ROSA Issues
      - Using Netlink gives better error messages. But everything fails
      - File exists errors. Some state doesn't get properly cleaned up on the error paths.
      - Next steps. Clean up state on failures
      - Open Issue for proper supporting XDP frags/dispatcher.
  - [Andre] https://github.com/bpfman/bpfman-operator/pull/426 is ready for review

## April 10th 2025 10:00 AM EDT/EST

:::info

- **Location:** https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e
- **Date:** April 10th 2025 10:00 AM EDT/EST
- **Host:** @dave-tucker
- **Participants:** Billy McFall, Andrew McDermott, Dave Tucker, Andre Fredette
:::

Agenda:

- Issue Triage
    - No new issues to triage
- Dosu bot closing stale issues
    - We can re-open if things aren't stale
    - But generally doing a good job so far
- KubeCon Update - @dave-tucker
    - Nearly 100 people at the project pavilion booth
    - Do this again at KubeCon North America
- [Billy] Operator Error reporting
    - Billy revisited error reporting post load/attach split work
    - Error messages are still too vague "An error has occurred"
    - To debug we start looking at logs
        - bpfman logs look ok
        - operator logs look awful. includes stack traces
    - [Dave] Lets defer this until after daemonsetless
    - Short term, can we at least remove the stack traces? Follow-up issue required. Action on Billy to do that.
- [Andy] dropping kube-rbac-proxy (downstream builds failing, and is deprecated)
    - https://github.com/kubernetes-sigs/kubebuilder/discussions/3907
    - Yes please! Lets have that.
- [Andy] I think metrics listener in OpenShift doesn't handle refreshed certificates
    - https://github.com/bpfman/bpfman-operator/issues/428
    - Lets implement that too please!
    - We probably don't expose any useful metrics, but lets do the work anyway!
- [Dave] Daemonset-less bpfman
    - Dave looking to start a PoC soon
- [Dave] init-containers
    - opens the path to deployment without an operator
    - if operator is there, you should use it
- [Andy] .openshift containerfiles upstream
    - do we want these upstream? we have no testing for them
        - Andy to get rid of them
- [Andre] Interface discovery
    - Testing on Red Hat OpenShift on AWS (ROSA) cluster using interface discovery. Seems to work fine on KIND. Issues on the ROSA cluster with hostnetwork containers and there's a PR upstream to fix it.
    - Potentially hit a bug or scale issue. Just debugging.
    - Need fixes in the library we use from NetObserv. Timeline unclear. Will sync with that project and see when it's going to be released
- [Billy] Leaving the project soon :sob: We need to help get his PRs reviewed and closed out as soon as possible.


