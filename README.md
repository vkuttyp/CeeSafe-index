# The CeeSafe package index

The registry of [CeeSafe](https://github.com/vkuttyp/CeeSafe) packages (the tooling specification's §4.6–§4.8, which
CeeSafe shares with Nova until its fork point). Each package has a file `packages/<name>.toml`, with one entry per
published version:

```toml
[[version]]
version = "1.4.0"
git = "https://github.com/someone/geometry.git"
commit = "3f2a9c0d…"                      # the full commit id
hash = "sha256:…"                         # of the commit's manifest and sources
dependencies = { shapes = "2.1" }         # its registry requirements
```

The code itself stays in each package's own repository: an entry only names a commit and the hash of what it holds.
`ceesafec fetch` reads this index (set `CEESAFE_INDEX=https://github.com/vkuttyp/CeeSafe-index` until it is
ceesafec's default), chooses versions, and records each commit and hash in the user's `ceesafe.lock`.

## Publishing a version

1. In the package: commit and push the version (its `ceesafe.toml` has `version = "…"` and `kind = "library"`, and
   every dependency is a registry dependency), then run `ceesafec publish`. It checks the package and prints the
   entry.
2. Append the entry to `packages/<name>.toml` here (create the file for a new package), in a pull request.

Entries are only ever appended. To withdraw a version, set `yanked = true` on it: locks that already use it keep
working, and no new resolution chooses it.

## Review

`ceesafec index-check` fetches each entry's commit, and checks:

- the commit's manifest has the entry's name and version;
- its hash and its registry dependencies are the entry's;
- each package's versions increase down the file.

Until ceesafec has a release, run it locally before merging (`ceesafec index-check` in a checkout of this repository);
a CI workflow will run it on every pull request once a release exists. A new package name is reviewed by a maintainer
before it is merged.
