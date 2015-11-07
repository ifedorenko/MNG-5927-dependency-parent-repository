# Maven Artifact Descriptor Cache Crosstalk

This project demonstrates the following scenario

There are two "remote" repositories

1. `repository-parent` contains artifact `dependency:dependency-parent`
2. `repository` contains artifact `depdendency:dependency`, which has `dependency:dependency-parent` parent

The test project is a multi-module project

* `module-a` depends on `depdendency:dependency` and has both `repository-parent` and `repository` repositories enabled
* `module-b` too depends on `depdendency:dependency` but only has `repository`

Building `module-b` by itself fails to resolve `depdendency:dependency` due to a failure to resolve `dependency:dependency-parent`. This is expected because `module-b` does not have access to `repository-parent`

Building both `module-a` and `module-b` in the same reactor (`module-a` is the first module) does not fail.

The problem appears to be in artifact descriptor cache implementation in `org.eclipse.aether.internal.impl.DefaultDependencyCollector#resolveCachedArtifactDescriptor`. The cache includes fully contructred (with inheritance and interpolation) artifact descriptor instances and does not honour parent pom repository visibility.

