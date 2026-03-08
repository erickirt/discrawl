---
summary: "Release checklist for discrawl (GitHub release binaries via GoReleaser + Homebrew tap update)"
---

# Releasing `discrawl`

Always do all steps below. No partial releases.

Assumptions:
- Repo: `steipete/discrawl`
- Binary: `discrawl`
- GoReleaser config: `.goreleaser.yaml`
- Homebrew tap repo: `~/Projects/homebrew-tap`

## 0) Prereqs

- Clean working tree on `main`
- Go toolchain from `go.mod`
- GitHub CLI authenticated
- CI green on `main`

## 1) Verify build + tests

```sh
go run github.com/golangci/golangci-lint/v2/cmd/golangci-lint@v2.5.0 run
go test ./... -coverprofile=coverage.out
go tool cover -func=coverage.out | tail -n 1
go build -o /tmp/discrawl ./cmd/discrawl
gh run list -L 5 --branch main
```

Coverage floor: `80%+`

## 2) Update changelog

Add a new section in `CHANGELOG.md`.

Example:

- `## 0.2.0 - 2026-03-08`

## 3) Commit, tag, push

```sh
git checkout main
git pull --ff-only origin main
git commit -am "release: vX.Y.Z"
git tag -a vX.Y.Z -m "Release X.Y.Z"
git push origin main --tags
```

## 4) Verify GitHub release assets

The tag push triggers `.github/workflows/release.yml`.

```sh
gh run list -L 5 --workflow release.yml
gh release view vX.Y.Z
```

Confirm assets exist for:

- `darwin_amd64`
- `darwin_arm64`
- `linux_amd64`
- `linux_arm64`
- `windows_amd64`
- `windows_arm64`

## 5) Update Homebrew tap

`discrawl` currently ships a source-build formula in `~/Projects/homebrew-tap/Formula/discrawl.rb`.

After tagging a real release:

1. switch the formula URL from the pinned source-commit tarball to the release tag tarball or release binaries
2. update `sha256`
3. test locally
4. commit + push `homebrew-tap`

Useful commands:

```sh
curl -L -o /tmp/discrawl.tgz https://github.com/steipete/discrawl/archive/refs/tags/vX.Y.Z.tar.gz
shasum -a 256 /tmp/discrawl.tgz
brew uninstall discrawl || true
brew install --build-from-source ./Formula/discrawl.rb
brew test discrawl
```

## Notes

- Build-time version stamping comes from `-X github.com/steipete/discrawl/internal/cli.version={{ .Version }}`
- If release workflow needs a rerun:

```sh
gh workflow run release.yml -f tag=vX.Y.Z
```
