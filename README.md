## Hood Digital Renovate Config

The shared Renovate preset for HoodDigital repos. The rules live in `default.json`.

### Use it

Add the preset to the repo's `renovate.json`:

```json
{
  "extends": ["github>HoodDigital/renovate-config"]
}
```

Keep anything specific to the repo in that file, after the `extends`: its own groups, schedule and PR body.

### What it sets

- **Security fixes skip the wait.** A fix for an advisory raises a PR on the next run, labelled `security`, with no minimum release age, no schedule and no dashboard approval.
- **Docker images are pinned by digest.** The first pin for a repo goes out at once, as one PR. After that a rebuild under the same tag raises a PR, and digest updates arrive as one grouped PR on Monday before 9am UTC.
- **Docker updates skip the release-age wait.** Image tags and digests carry no release date, so the 3-day wait would hold them forever.
- **NuGet minor and patch updates arrive as one PR,** titled "Update NuGet packages", so a repo builds once for the batch. Majors still raise their own PR after dashboard approval, and a .NET SDK update to `global.json` keeps its own PR.
- **Auth0 packages move together, with no wait.** `Auth0.Core`, `Auth0.AuthenticationApi` and `Auth0.ManagementApi` go in one PR, majors included, as soon as any of them has an update. The 3-day minimum release age does not apply to them. The packages keep their own version numbers, so the PR can move one to 10.x and another to 7.x.
- **Everything else** is the `config:recommended` base, a 3-day minimum release age, dashboard approval for majors, and at most 10 open PRs and 2 new ones an hour.

### Known limits

- **The Auth0 PR waits for approval when one package has a major.** Tick it on the Dependency Dashboard to raise it.
- **A security fix for one Auth0 package raises two PRs at once.** Renovate builds each advisory PR for that one package and cannot be told to group it, so you get the pair PR and a lone one for the fixed package. Merge the pair PR, never the lone one. Renovate is expected to close the lone PR once the default branch has the fixed version.

### Check a change

Renovate 44 needs pnpm 11:

```sh
pnpm dlx --allow-build=re2 --package renovate renovate-config-validator --strict --no-global default.json
```
