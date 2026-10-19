# looky_devops_shared_workflows

## Explicit image tags

`build_and_push.yaml` (Amazon ECR) and `ec_build_and_push.yaml` (Harbor) accept
an optional `image_tag` input. It sets the full base image tag, ignores
`tag_prefix`, and skips automatic version calculation and Git tag creation.
When omitted or empty, the existing automatic versioning behavior is preserved.

For example, a caller triggered by a GitHub Release can pass:

```yaml
with:
  image_tag: ${{ github.event.release.tag_name }}
  tag_suffix: "-$(date +%s)"
```

For a release named `yc-0.1.58`, this produces `yc-0.1.58-<timestamp>`.
`tag_suffix` is appended as before, so keep the timestamp suffix for FluxCD
image automation. Amazon ECR also retains its additional tag without the suffix.
The workflow's `tag` output and the `APP_VERSION` build argument both contain
the selected base tag, without the suffix.

Use a workflow ref that includes the `image_tag` input. Release names must be
valid Docker tags and match the caller's Flux image policy; any prefix conversion
(for example, `test-` to `prod-`) is the caller's responsibility.
