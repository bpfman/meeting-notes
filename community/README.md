# bpfman Community Meeting Notes 2025

This document contains the notes of the bpfman community meeting.

This document is best viewed and edited online: [![hackmd-github-sync-badge](https://hackmd.io/jZ8odWiqRGCQYqumtExJCQ/badge)](https://hackmd.io/jZ8odWiqRGCQYqumtExJCQ)

[TOC]

## May 1st 2025 10:00 AM EDT/EST

:::info

- **Location:** https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e
- **Date:** May 1st 2025 10:00 AM EDT/EST
- **Host:** @dave-tucker
- **Participants:**
:::

Agenda:
  - Issue Triage

## April 24th 2025 10:00 AM EDT/EST

:::info

- **Location:** https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e
- **Date:** April 24th 2025 10:00 AM EDT/EST
- **Host:** @dave-tucker
- **Participants:** Dave Tucker, Andy McDermott, Andre Fredette, Billy McFall, Mohamed Mahmoud
:::

Agenda:

- Issue Triage
    - XDP Dispatcher should support fragment handling bpfman/bpfman#1527
            - High priority, Medium, Lets get it done soon
    - Investigate XDP attach/detach behaviour bpfman/bpfman#1528
        - In some cases, we did get successful attachments on some interfaces. Not exactly sure why since they all have large MTUs. Possible a kernel bug, or in the driver. Requires more investigation. Medium.
    - Interface Discovery due to duplicate network namespaces bpfman/bpfman-operator#433
        - Fix Posted. PR needs to be reviewed.
    - Dispatchers and BPF state not always cleaned up after CRD delete bpfman/bpfman#1537
            - PR up and reviewed. Ready for another look
    - Don't call init_image_manager if we don't need to bpfman/bpfman#1538
        - Potential performance improvement - move down closer to where it's used.
        - Dispatchers are a lot slower than other programs.
        - Small fix for now
        - High, Small, Andre will take care of it
    - Use official release of netobserv-ebpf-agent when PR is included bpfman/bpfman-operator#434
        - Can use SHA from main since the PR has merged
        - Need to do a release on netobserv-ebpf-agent (later on)
- Undeploy issue
    - Security Profile Operator issue was for any SELinux example programs not being undeployed (examples-selinux). It hangs, because SPOs finalizer doesn't get cleaned up.
        - SPO we made it a dependency of bpfman via OperatorHub. It got installed in our namespace. They had a bug when they went to fetch DS they got the wrong one so they never finish reconcile and all things stayed in Pending state. 
        - On delete, SPO hung because the whatever added the finalizer to a object disappeared
    - Andy's undeploy issues is because there's an io.bpfman finalizer on our D/S
        - Only via Make, not via OperatorHub
        - Andy has a fix in progress.
    - Privileges in OpenShift need looking into - Dave to do when he has time.
    - 2/3 node lock up on repeated deploy/undeploy on OpenShift. Is this a bpfman issue? Or potentially a logging issue?
    - Removing kube-rbac-proxy is in flight also.
- [Ramon] Update on the uprobe issue. Still the same issue deploying on K8s + when deploying locally from the CLI also. Changed the order in which the programs where ordered/registered in the Go SDK code. Now it works fine :exploding_head: - order of execution should not be important... but it is. Still using bpfman 5.6. 
- [Mohamed] Aya Split BTF (will try and include in the next bpfman release and the next aya release)
- [Mohamed] IPSEC in NetObserv shows that input/output probes are loaded, but one side not being hit. Dave T to debug at some point.
- [Mohamed] API review comments
    - Billy has description changes that need doing
    - Then there's lots more work to sync the upstream changes back to downstream and re-request review
        

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

## 2025-04-03

Attendees: Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat), Andy McDermott (Red Hat)

Agenda:

* Triage  
  * None

## 2025-03-27

Attendees: Billy McFall (Red Hat), Dave Tucker (Red Hat), Andre Fredette (Red Hat), Ramon Marquez (Graylog)

Agenda:

* Triage  
* API Review:  
  * \[Andre\] Limits to CRD fields.  
  * \[Andre\] State CRDs: Move fields from spec to status  
* \[Andre\] API Options:  
  * \+kubebuilder:default:=foo  
  * \+optional  
  * omitempty  
  * \+required  
  * \+kubebuilder:validation:Required

## 2025-03-20

Attendees: Billy McFall (Red Hat), Dave Tucker (Red Hat), Andre Fredette (Red Hat), Andrew McDermott (Red Hat)

Agenda:

* Triage  
* API Review  
  * \[Billy\] Working through enhancing all the field descriptions: [https://gist.github.com/Billy99/b871e60f04944d4b03c9c0106d2c8a43](https://gist.github.com/Billy99/b871e60f04944d4b03c9c0106d2c8a43)   
  * \[Andre\] What to do about Fentry/Fexit program links in CRDs?  
  * \[Mohamed\]  
* \[Mohamed\] Network Observability Operator \- Run unprivileged  
* \[Billy\] CLI Proposal (Includes possible new GetLinks() bpfman API)  
  * [CLI Load/Attach Rework](https://docs.google.com/document/d/1IREMBGVpg-qCQOjZJUJqHfUjoP2twB2buIvxrxmiB_I/edit?tab=t.0#heading=h.xl2bf0yvn4t6)  
* \[Andre\] Issue changing netns when path is /var/run/netns/\<netns\_name\>  
  * Update: Fixed after this meeting.  
* \[Andy\] SQLite DB still in progress

## 2025-03-13

Attendees: Billy McFall (Red Hat), Dave Tucker (Red Hat), Andre Fredette (Red Hat), Ramon Marquez (Graylog)

Agenda:

* Triage  
* \[Mohamed\] API Review  
  * Need help

## 2025-03-06

Attendees: Billy McFall (Red Hat), Dave Tucker (Red Hat), Ramon Marquez (Graylog), Andre Fredette (Red Hat)

Agenda:

* Triage  
* Now using CNCF Zoom for Community Meetings:  
  * bpfman LF Calendar: [https://zoom-lfx.platform.linuxfoundation.org/meetings/bpfman?view=week](https://zoom-lfx.platform.linuxfoundation.org/meetings/bpfman?view=week)   
  * Zoom: https://zoom-lfx.platform.linuxfoundation.org/meeting/95637056950?password=be520c6f-362b-46fc-8f9b-eb4eaa81626e  
  * Setup Slack Reminder (In case someone needs to change in the future):  
    /remind \#bpfman "Reminder: Community meeting in 30 minutes \<https://zoom-lfx.platform.linuxfoundation.org/meetings/bpfman?view=week | here\>" at 9:30 am every Thursday  
    /remind list  
* FYI: SecurityProfileOperator has not posted v0.9.0 in OperatorHub yet because it is failing an upgrade test (see [failed test](https://github.com/k8s-operatorhub/community-operators/actions/runs/13525187621/job/37793970200?pr=5799)). When bpfman goes to post a v0.6.0, we may have a similar issue since we changed our API. Not 100% what the upgrade test is doing, but we should get ahead of this in bpfman when cutting the next release.  
* 

## 2025-02-27

Attendees: Billy McFall (Red Hat), Dave Tucker (Red Hat), Ramon Marquez (Graylog)

Agenda:

* Triage  
* \[Ramon\] Multiple kprobe programs and sharing maps issue  
  * Documented in slack thread: [https://kubernetes.slack.com/archives/C04UJBW2553/p1740561760176319](https://kubernetes.slack.com/archives/C04UJBW2553/p1740561760176319)   
  * Ramon to send sample programs and yamls so we can reproduce.  
* \[Dave\]  
  PSA: bpfman is going to Kubecon EU London
  
  We have a booth in the project pavillion on Weds Apr 2 10:45 \- 15:00\!  
  If you are able to help out, please let me know by completing the form below.  
  This is open to everyone \- maintainers, contributors and even just fans of the project
  There may also be some limited edition swag available for booth helpers \- just saying  
  [https://forms.gle/HnbwTt68HcNiy6iT7](https://forms.gle/HnbwTt68HcNiy6iT7)  

  [How to ConvinceYour Boss](https://events.linuxfoundation.org/kubecon-cloudnativecon-europe/attend/convince-your-boss/)

## 2025-02-20

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat),  Dave Tucker (Red Hat), Ramon Marquez (Graylog)

Agenda:

* Triage  
* Welcome Ramon\!  
  * Using bpfman to deploy uprobes and kprobes in K8s  
  * It’s been good and it works\!  
  * Initial friction with documentation  
  * FEATURE REQUEST: Using ebpf-go. You use \`link\` to link to the write system call it tries the various arch specific prefixes. Can we do that in bpfman?  
    * [https://github.com/bpfman/bpfman/issues/1435](https://github.com/bpfman/bpfman/issues/1435)   
* \[Billy\] `make generate` fails to generate clientset  
  * Haven’t fixed yet, but think it’s my GO version  
  * [https://github.com/msherif1234/general-debug-tools/blob/main/update-go.sh](https://github.com/msherif1234/general-debug-tools/blob/main/update-go.sh)  
  * [https://github.com/udhos/update-golang](https://github.com/udhos/update-golang)  
* \[Dave\] i’m merging bpfman PRs \- lets hope it doesn’t break the operator 😂  
* \[Dave\] bpfman CI rework in the pipeline, will also look at bpfman-operator because of 👆  
* \[Andre\] I’m working on integrating [https://github.com/bpfman/bpfman-operator/pull/347](https://github.com/bpfman/bpfman-operator/pull/347) with [https://github.com/bpfman/bpfman/pull/1354](https://github.com/bpfman/bpfman/pull/1354)   
* \[Dave\] oci-client and sigstore-rs work \- we’re going sync soon without nasty hacks 🎉

## 2025-02-13

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat),  Andrew McDermott (Red Hat), Dave Tucker (Red Hat), Daniel Mellado (Red Hat)

Agenda:

* Triage  
* \[Dave\] Load/Attach Split bpfman PR overview

## 2025-02-06

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat),  Andrew McDermott (Red Hat), Dave Tucker (Red Hat)

Agenda:

* Triage  
* \[Billy\] Use the CNCF Zoom account for meetings instead of gmeet  
  * I dropped the ball. I said I would follow-up. WebSite says: “Most services can be requested through a Service Desk ticket, unless otherwise noted below.” Is that just the github issues? Couldn’t find any Zoom related issues in the open or closed issues.  
* \[Billy\] CNCF Snyx Email \- “if you can confirm that Snyk is set up, we can mark this onboarding checklist complete”. Dave, they were hijacking your email so are we uninstalling or was that addressed?  
  * Andre to follow up.  
* 

## 2025-01-30

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat),  Andrew McDermott (Red Hat), Dave Tucker (Red Hat), Shane Utt (Red Hat)

Agenda:

* Triage  
* \[Dave\] Use the CNCF Zoom account for meetings instead of gmeet  
  * Added [https://github.com/bpfman/bpfman/issues/1392](https://github.com/bpfman/bpfman/issues/1392)   
* \[Andy\] v3 versus v4 github upload/download-artifact \- AFAICT, we’re using v4   
  * [https://github.com/bpfman/bpfman-operator/issues/348](https://github.com/bpfman/bpfman-operator/issues/348)  
  * [https://github.com/bpfman/bpfman/issues/1358](https://github.com/bpfman/bpfman/issues/1358)  
* \[Andre\] I’m seeing the following error  
  * $ bpfman image build \-c ./tests/integration-test/bpf/.output/app-test.bpf/ \--tag quay.io/bpfman-bytecode/app-test \--container-file ./Containerfile.bytecode.multi.arch  
  * Error: Failed to parse bytecode: "./tests/integration-test/bpf/.output/app-test.bpf/bpf\_s390\_bpfeb.o" with error: BTF error  
* \[Andre\] clang check seems to be using clang defaults.  
* \[Dave\] quay.io stats?  
* \[MM} Can we have the passed in args to function when use kretprobe ?

## 2025-01-23

Meeting canceled due to conflicts.

## 2025-01-16

Attendees: Billy McFall (Red Hat), Mohamed Mahmoud (Red Hat), Dave Tucker (Red Hat)

Agenda:

* Triage  
* \[Billy\] SLED behavior \- seeing some flakiness with PR where sled inserts were rearranged.  
* (this can go last) \[shane\] growing the community

## 2025-01-09

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Mohamed Mahmoud (Red Hat), Daniel Mellado (Red Hat), Dave Tucker (Red Hat), Shane Utt (Red Hat)

Agenda:

* Triage  
* v0.5.5 has been released  
  * Not posted on OperatorHub.io, see [Issue\#5502](https://github.com/k8s-operatorhub/community-operators/issues/5502)  
* New pr from Dependabot ([\#359](https://github.com/bpfman/bpfman-operator/pull/359)) also requires Go v1.23.  
  * I think it replaced the previous one  
  * Are Dependabot updates on hold now until we do the Go upgrade?  
    Answer: Yes  
* Open PR review  
* Load/Attach Split Status

## 2025-01-02

Attendees: Billy McFall (Red Hat), Andre Fredette (Red Hat), Andrew McDermott (Red Hat)

Agenda:

* Release v0.5.5 discussion
