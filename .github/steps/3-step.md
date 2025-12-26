## Step 3: Dynamic Tagging with Metadata

### 📖 Theory: Automated Tagging and Versioning

Hardcoding image tags like `:latest` is not sustainable for real workflow.

 The `docker/metadata-action` automatically generates appropriate tags and labels based on Git context - creating different tags for branches, pull requests, and version tags.

This action extracts information from Git references and GitHub events to create meaningful Docker image metadata. It supports semantic versioning patterns, branch-based tagging, and pull request labeling, making your container workflow more professional and maintainable.
on**: Creates OCI-compliant labels for better image documentation

#### References

- [docker/metadata-action documentation](https://github.com/docker/metadata-action#about)
- [Docker image tagging best practices](https://docs.docker.com/develop/dev-best-practices/)

### ⌨️ Activity: Docker metadata extraction

1. Edit `.github/workflows/docker-publish.yml`.
1. Update the workflow trigger to include `tags` matching `v*` pattern and pull requests.

    ```yaml
    on:
      push:
        branches:
          - main
        tags:
          - "v*"
      pull_request:
        branches:
          - main
    ```

1. Add a step to extract metadata (tags, labels) for Docker images

  Place it after the `docker/login-action` step.

  ```yaml
  - name: Extract metadata (tags, labels) for Docker
    id: meta
    uses: docker/metadata-action@v5
    with:
      images: ghcr.io/{{ full_repo_name | lower }}/stackoverflown
  ```

1. Update the `docker/build-push-action` step to use the generated tags and labels.

   ```yaml
   - name: Build and push Docker image
     uses: docker/build-push-action@v5
     with:
       context: .
       push: true
       tags: {% raw %}${{ steps.meta.outputs.tags }}{% endraw %}
       labels: {% raw %}${{ steps.meta.outputs.labels }}{% endraw %}
   ```

1. Commit your changes and push a new tag to test the versioning (e.g., `v1.0.0`).

   ```bash
   git commit -am "Add docker/metadata-action for tagging and labeling" && git push
   git tag v1.0.0 && git push origin v1.0.0
   ```

<details>
<summary>Having trouble? 🤷</summary><br/>

- Make sure you gave the metadata step an `id: meta` so you can reference its outputs.
- Ensure the `tags` trigger pattern matches the tag you push (e.g., `v*.*.*` matches `v1.0.0`).
- Check that you are using `{% raw %}${{ steps.meta.outputs.tags }}{% endraw %}` and `{% raw %}${{ steps.meta.outputs.labels }}{% endraw %}` correctly.

</details>
