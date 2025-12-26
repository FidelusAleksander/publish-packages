## Step 2: Enhanced Docker Actions

### 📖 Theory: Specialized Docker Actions

<!-- While basic Docker commands work, specialized GitHub Actions provide significant improvements for container workflows. The `docker/setup-buildx-action` enables BuildKit features like advanced caching, multi-platform builds, and improved performance.

The `docker/build-push-action` replaces manual `docker build` and `docker push` commands with a more configurable and efficient solution. It integrates seamlessly with Buildx and provides better error handling, caching strategies, and build optimization.

- **Buildx**: Docker's build engine with extended features beyond standard Docker build
- **Build context**: Defines which files and directories are sent to the Docker daemon
- **Push configuration**: Control when and how images are pushed to registries
- **Performance**: Buildx provides better caching and parallel build capabilities -->

#### References

- [Docker Buildx documentation](https://docs.docker.com/buildx/)
- [docker/build-push-action usage](https://github.com/docker/build-push-action#usage)

### ⌨️ Activity: See and run your docker image

If everything went correctly, we should now we able to see your image, pull it and run it!

1. Go to your repository [main page](https://github.com/{{ full_repo_name }})
1. On the right side click `{{ full_repo_name | lower }}/stackoverflown`
<!-- TODO: ADD IMAGE -->
1. Copy the command that starts with `docker pull ...`
1. Back in your codespace, run that command in the terminal to download the image from the container registry
1. Verify the image is available locally by running:

   ```bash
   docker images
   ```

1. Let's create a Docker container from that image and see the stackoverflown app running!

   ```bash
   docker run -p 8080:80 ghcr.io/{{ full_repo_name | lower }}/stackoverflown:latest
   ```

1. You can access the application through the `Ports` tab - on port `80`

   > ✨ Take a moment to play the game!

1. You can stop the application from running by hitting `Ctrl + C` (`Cmd + C` on Mac) back in the terminal

> [!NOTE]
> Throughout this exercise, we'll create different versions of the image. You can always use these same steps to pull and run any version you create, even if not explicitly instructed.

### ⌨️ Activity: Implement Docker Build Actions

1. Edit `.github/workflows/docker-publish.yml`.
1. Remove your existing `Build and push Docker image` step with `docker` commands. We will replace that with open source actions.
1. Add these following two steps in place of the step you just removed

   ```yaml
   - name: Set up Docker Buildx
     uses: docker/setup-buildx-action@v3
   - name: Build and push Docker image
     uses: docker/build-push-action@v6
     with:
       context: .
       push: true
       tags: |
         ghcr.io/{{ full_repo_name | lower }}/stackoverflown:latest
         ghcr.io/{{ full_repo_name | lower }}/stackoverflown:{% raw %}${{ github.sha }}{% endraw %}
   ```

   Ensure the yaml indentation is setup correctly!

   > 💡 **Tip:** You can run `actionlint` command in the terminal to see if the workflow is properly formatted.

1. Commit and push your changes to the `main` branch.

<details>
<summary>Having trouble? 🤷</summary><br/>

- Ensure you removed the previous `run` step with `docker build` and `docker push`.
- Check that `push: true` is set in the `docker/build-push-action` configuration.
- Verify that `docker/setup-buildx-action` is placed before `docker/build-push-action`.

</details>
