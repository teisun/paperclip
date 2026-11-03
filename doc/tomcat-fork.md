# Tomcat fork workflow

```text
paperclipai/paperclip (upstream/master)
          | fetch + merge
          v
feat/tomcat-agent -- validate + push --> teisun/paperclip (origin)
          ^                                    |
          +------------- fetch + merge --------+
```

Select `feat/tomcat-agent` to use the Tomcat integration. The default `master`
branch stays upstream-only. Adding two remotes does not automatically merge or
upload changes.

Start with a clean working tree on `feat/tomcat-agent`.

## Changes from another machine

```sh
git fetch origin
git merge --ff-only origin/feat/tomcat-agent
```

If both sides have new commits, inspect them and use a normal
`git merge origin/feat/tomcat-agent`, resolve conflicts and validate. Do not reset
or force-push away either history.

## Updates from the source project

```sh
git fetch upstream
git merge upstream/master
# Resolve conflicts and validate before uploading.
git push origin HEAD:refs/heads/feat/tomcat-agent
```

`origin` is `https://github.com/teisun/paperclip.git`; `upstream` is
`https://github.com/paperclipai/paperclip.git`. The repository-local push default
is `origin`. Push only the integration branch, not backup references or tags.

## Tomcat setup and operation

- Use an ACP-enabled Tomcat executable on the same local filesystem as the task
  workspace. Remote execution environments are not supported by `tomcat_local`.
- Choose Tomcat in Add agent. The model is optional. Advanced settings select the
  executable, an optional full ACP command and permission-response policies.
  `Save & exit` saves the existing setup record; reopening it restores these
  settings and lets you finish the same agent rather than creating another one.
- The runtime check verifies executable/service readiness without sending a model
  prompt. A passing check does not certify model credentials or model quality.
- ACP approval replies do not override Tomcat's own hard-deny rules. To make
  Tomcat ask for approval, configure its existing `primitive.auto_confirm=false`.
- The adapter uses the existing ACP engine, task-scoped skills/MCP/environment
  delivery and continuation ownership checks. It does not add a native Runner
  provider or a second draft store.

Before updating a retained instance, stop application writes and back up its
PostgreSQL data, configuration, local master key and attachment storage together.
Rehearse the normal migrations on a restored copy before migrating the original.
Preserve the instance ID, configuration and data directory; do not rerun onboarding
or create an empty instance in place of an upgrade. Keep backups local and private.

Use the Node and pnpm versions declared in this checkout's `package.json` and a
frozen-lockfile install. This fork supports the local source-based workflow; its
private Tomcat workspace package has not been made npm-publishable, and the
Docker/public-release packaging lanes are separate, unvalidated work. Do not
publish packages, images or Releases to synchronize source code.
