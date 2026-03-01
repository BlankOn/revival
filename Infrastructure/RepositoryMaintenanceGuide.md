# Repository Maintenance Guide

## Rules and Reasonings

### 1. No Merged Repositories Between Release

We will isolate repositories for each release. Based on our experience with the Tambora repository incident, resolving issues becomes significantly more difficult when repositories are merged. Until we have sufficient resources to maintain the infrastructure, we will not adopt a merged repository approach.

Benefits of isolated repositories:
- Simpler backup management
- Easier decommissioning when a release is no longer supported
- Issue resolution without impacting other releases

Drawback:
- Higher storage requirements to maintain a dedicated VM for each release (including overhead).

### 2. Dedicated Staging Environment for Each Release

Each release should have its own staging environment to ensure that production repositories remain stable. Before syncing the development repository to the production repository, we must properly test the development repository.

Process:
1. Back up the development repository
2. Sync from upstream or add required packages
3. Perform thorough testing
4. Back up the production repository
5. Sync the development repository to the production repository
6. Announce the updates

---

References:
- https://github.com/BlankOn/revival/blob/main/Infrastructure/Reprepro.md
