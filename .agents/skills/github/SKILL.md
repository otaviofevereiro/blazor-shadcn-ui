---
name: github
description: >
  One GitHub Release per master commit, plus GHCR image and Compose artifacts when the
  repo has them. Use when GitHub Actions workflows, a Dockerfile, or a Compose file are in play.
---

# GitHub releases

**When:** GitHub Actions workflows, a Dockerfile, or a Compose file are in play.

- Every push to `master` publishes one GitHub Release for that commit. The tag is `sha-<full commit sha>`. Create it with `gh release create` and `--target` set to that commit. A rerun uploads assets onto the same release.
- The release job waits until the repo's build and tests pass. It needs `contents: write` and `GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}`. A tag push made with that token does not start another workflow run.
- Attach the build outputs the repo already produces. Keep secrets, `.env` files, and credentials out of the release.
- The release tag stays `sha-<sha>`. Version tags `v*` stay with production deploys in `azure-cicd`. Publishing a release does not approve an environment deploy.
- Pin third-party actions to a commit SHA.
- When the repo has a Dockerfile, build that image and push it to `ghcr.io`. Owner and image name are lowercase. Tag the image with the release tag. The job needs `contents: read` and `packages: write`. Log in to `ghcr.io` with `github.actor` and `GITHUB_TOKEN`. Record `image@digest` on the release.
- Publish a build provenance attestation (`actions/attest`, `push-to-registry: true`) when the repository is public or owned by an organization. That step needs `attestations: write` and `id-token: write`. Skip it when the repository is private and the owner is a user: the attestations API returns `Feature not available for user-owned private repositories`. The release still records the image digest.
- When the repo has a Compose file (`compose.yaml`, `compose.yml`, `docker-compose.yaml`, or `docker-compose.yml`), push every service that has `build:` first, then point those services at the immutable `ghcr.io` image. Attach the file from `docker compose config --resolve-image-digests` to the release. If the file has no bind mount and no local `include`, also run `docker compose publish -y --resolve-image-digests` to `ghcr.io/<owner>/<repo>:<release tag>`. Docker refuses OCI publish for bind mounts, local includes, and a service that has `build:` and no `image:`. Do not pass `--with-env`.

```yaml
# BAD
on: { push: { branches: ["*"] } }
# gh release create v1.2.3

# GOOD
on: { push: { branches: ["master"] } }
# gh release create "sha-${GITHUB_SHA}" --target "$GITHUB_SHA" --generate-notes ./dist/*
# Dockerfile → ghcr.io/<owner>/<image>:sha-<sha>
# actions/attest when the repo is public or organization-owned
# Compose → docker compose config --resolve-image-digests, then publish when Docker allows it
```
