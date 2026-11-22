---
title: Support policy
description: Which KyPanel versions and operating systems get fixes, and how features are retired.
---

## Versions

KyPanel releases are numbered `MAJOR.MINOR.PATCH`:

- a **patch** release (0.2.1) only fixes problems;
- a **minor** release (0.3.0) brings new features;
- a **major** release (1.0.0, 2.0.0) changes something you use in a way that may need action from you. Its changelog starts with an **Action required** section.

The latest stable minor release receives every fix. The previous minor release receives security fixes until the next minor release is published. Servers with automatic updates on always run a supported version.

Every release is listed in the [changelog](/changelog/), including the ones that stayed on the beta channel.

## Channels

- **Stable** (default) receives a release once it has run on beta. Recommended for every production server.
- **Beta** receives each release first. Choose it on a server where you want new features as soon as they're out.

A server never moves to an older release: switching from beta to stable waits until stable catches up. See [Updates](/updates/).

## Operating systems

Each supported distribution is supported until its vendor's end of life, and KyPanel announces the end in the changelog at least one minor release before. See [Requirements](/requirements/) for the list.

## Retiring a feature

A feature or API that goes away is first listed under **Deprecated** in the changelog, at least one minor release before it's removed. From version 1.0, a removal that breaks something only happens in a major release.

Before 1.0, a minor release (0.3.0) may also need action from you. When it does, its changelog starts with **Action required**, and nothing is removed without a **Deprecated** notice in an earlier release.
