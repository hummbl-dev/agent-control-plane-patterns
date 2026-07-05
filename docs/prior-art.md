# Prior Art and Adjacent Ecosystem

This document surveys **public** prior art for agent control-plane patterns. It
is a reference reading list, not an endorsement, and not a canon.

The patterns surveyed here come primarily from the Kubernetes ecosystem, which
has the most mature, publicly documented body of control-plane architecture. The
intent is to ground agent control-plane patterns in established vocabulary and
mechanisms, not to claim novelty over any of them.

## Non-canon note

This survey is **non-canon**. It is a candidate reading list for v0.1. Entries
are summarized from public documentation; canonical definitions live in the
linked upstream sources. Where this document and an upstream source disagree,
the upstream source wins.

This repo does not claim ownership of any term, pattern, or project listed
below.

## Public prior art

### Kubernetes Control Plane

- **What it does:** The Kubernetes control plane is the set of components that
  manage the cluster's desired state: the API server, etcd, the scheduler, the
  controller manager, and the cloud-controller manager.
- **Relevance:** The canonical reference for "control plane" as a separable,
  declarative, reconciling layer that drives a data plane toward desired state.
  Agent control-plane patterns inherit the desired-state / observed-state /
  reconciliation framing directly from this model.
- **Public docs:** https://kubernetes.io/docs/concepts/overview/components/

### OpenShift Operator Framework

- **What it does:** Operator Framework is a set of tools and patterns for
  building, packaging, and managing Kubernetes-native applications as Operators.
  Includes the Operator SDK, OLM (Operator Lifecycle Manager), and the Operator
  Registry.
- **Relevance:** Demonstrates how control-plane-style controllers can be
  packaged, distributed, and lifecycle-managed themselves — a meta-control-plane
  for controllers. Relevant to how agent control-plane packets might be
  versioned, distributed, and lifecycle-managed.
- **Public docs:** https://operatorframework.io/

### Crossplane control plane

- **What it does:** Crossplane is a control plane built on Kubernetes that
  reconciles cloud and external infrastructure resources using CRDs and
  controllers. It treats infrastructure as Kubernetes-native objects.
- **Relevance:** A clean example of a control plane that reconciles *external*
  systems (not just in-cluster workloads), which maps closely to agent
  control planes that reconcile external tools and services.
- **Public docs:** https://docs.crossplane.io/

### Istio Control Plane (Istiod)

- **What it does:** Istiod is the unified Istio control plane, combining
  service discovery, configuration distribution, certificate issuance, and
  xDS configuration for the data-plane proxies (Envoy).
- **Relevance:** A strong example of a control plane that drives a separate
  data plane (sidecar proxies) via a configuration distribution protocol (xDS),
  with explicit desired-state and observed-state semantics. Relevant to agent
  control planes that drive agent runtimes via configuration.
- **Public docs:** https://istio.io/latest/docs/ops/deployment/architecture/

### Knative Controller

- **What it does:** Knative Serving and Eventing use Kubernetes controllers and
  CRDs to manage serverless workloads and event flows, reconciling revisions,
  routes, services, and triggers.
- **Relevance:** Demonstrates reconciliation over higher-level abstractions
  (revisions, routes, event triggers) on top of Kubernetes primitives — a useful
  analog for reconciling agent tasks, runs, and sessions.
- **Public docs:** https://knative.dev/docs/

### AWS ACK (AWS Controllers for Kubernetes)

- **What it does:** ACK is a set of Kubernetes controllers that manage AWS
  resources directly from Kubernetes using CRDs, reconciling AWS service state
  against Kubernetes-native desired state.
- **Relevance:** Another external-system reconciliation example, with one
  controller per AWS service. Relevant to per-capability agent controllers that
  reconcile external tool state.
- **Public docs:** https://aws-controllers-k8s.github.io/community/docs/

### Cluster API

- **What it does:** Cluster API provides declarative APIs for creating,
  managing, and upgrading Kubernetes clusters themselves, using CRDs and
  controllers (Cluster, Machine, MachineDeployment, etc.).
- **Relevance:** A meta-control-plane for managing control planes. Demonstrates
  layered reconciliation (a control plane managing other control planes), which
  is directly relevant to layered agent control planes.
- **Public docs:** https://cluster-api.sigs.k8s.io/

## Adjacent ecosystem

These are the building blocks and tooling commonly used to implement the
control-plane patterns above.

### Operator SDK

- **What it does:** A toolkit for building Kubernetes Operators, scaffolding
  controllers, CRDs, API types, and project structure.
- **Relevance:** Reference tooling for producing control-plane-style controllers
  with a standard layout. Relevant to how agent control-plane packets might
  scaffold their controller specs.
- **Public docs:** https://sdk.operatorframework.io/

### Kubebuilder

- **What it does:** A framework for building Kubernetes APIs using CRDs and
  controllers, providing project scaffolding, code generation, and a build/test
  workflow.
- **Relevance:** The upstream scaffolding that Operator SDK builds on. Defines a
  standard controller project layout and lifecycle.
- **Public docs:** https://book.kubebuilder.io/

### Controller Runtime

- **What it does:** The library that underlies both Kubebuilder and Operator SDK:
  provides the manager, controllers, reconcilers, client, cache, and leader
  election primitives.
- **Relevance:** The concrete implementation of reconciliation loops, workqueues,
  caching, and leader election that the prior-art control planes rely on. The
  vocabulary in this repo (reconciliation, workqueue, leader election) maps
  directly to Controller Runtime concepts.
- **Public docs:** https://pkg.go.dev/sigs.k8s.io/controller-runtime

### Informers

- **What it does:** Kubernetes informers provide a watch-based, cached view of
  cluster resources, with resync intervals and event handlers, used by
  controllers to observe state efficiently.
- **Relevance:** The observed-state side of the control loop. Agent control
  planes need an equivalent mechanism for observing agent/runtime state without
  polling.
- **Public docs:** https://pkg.go.dev/k8s.io/client-go/informers

### Reconciliation loops

- **What it does:** A reconciliation loop continuously compares desired state
  to observed state and takes action to reduce drift, idempotently and
  level-triggered rather than edge-triggered.
- **Relevance:** The core mechanism of every control plane above. The central
  pattern this repo is named after.
- **Public docs:** https://kubernetes.io/docs/concepts/architecture/controller/

### CRDs (Custom Resource Definitions)

- **What it does:** CRDs extend the Kubernetes API with user-defined resource
  types, each with a schema (OpenAPI v3), status, and controller.
- **Relevance:** The packaging mechanism for control-plane-managed objects. The
  v0.1 packet schema in this repo is an analog: a declarative description of a
  control-plane-managed thing.
- **Public docs:** https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/

## Vocabulary

Working definitions for this repo. These are candidate definitions for
discussion; canonical definitions live in the upstream sources linked above.

- **Control plane:** The layer that holds desired state, observes actual state,
  and reconciles the two. Separable from the data plane it drives.
- **Data plane:** The layer that executes work according to configuration
  received from the control plane. In Kubernetes, workloads and proxies; for
  agents, the agent runtimes and tools.
- **Reconciliation:** The act of comparing desired state to observed state and
  taking idempotent action to reduce drift.
- **Desired state:** The declarative intended state supplied to the control
  plane (e.g., a spec).
- **Observed state:** The actual state reported back from the data plane
  (e.g., a status).
- **Drift:** The difference between desired state and observed state.
- **Controller:** A process that runs a reconciliation loop for one or more
  resource types.
- **Operator:** A controller (or set of controllers) that manages an application
  or service, including its operational domain knowledge.
- **Custom resource:** A user-defined resource type (via CRD) managed by a
  controller.

## Key concepts

- **Control loops** — the reconciliation loop as the fundamental unit.
- **Desired vs observed state** — the two inputs to reconciliation.
- **Reconciliation** — idempotent, level-triggered drift reduction.
- **Leader election** — ensuring a single active controller instance to avoid
  split-brain reconciliation.
- **Workqueues** — the rate-limited, deduplicating queues that drive
  reconciliation attempts.
- **Finalizers** — pre-delete hooks that prevent resource deletion until
  cleanup completes.
- **Status conditions** — structured, typed observations about resource state
  reported back in status.
