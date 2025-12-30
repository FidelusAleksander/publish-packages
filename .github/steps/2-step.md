## Step 2: Level up with Docker Actions

Congrats! That commit you just did should have triggered the first run of your workflow and published a Docker image to the GitHub Container Registry

Let's see how to download that image and enhance the workflow with open source docker actions.

### 📖 Theory: Specialized Docker Actions

Similarly to `docker/login-action` there are also other open source actions that provide significant improvements for container workflows.

| Action                       | Benefits                                                                           |
| :--------------------------- | :--------------------------------------------------------------------------------- |
| `docker/setup-qemu-action`   | Enables building for different architectures (e.g., ARM64) via emulation           |
| `docker/setup-buildx-action` | Enables multi-platform builds, cache export, and full BuildKit support             |
| `docker/build-push-action`   | Supports multi-platform builds, secrets, remote cache, and advanced build features |

### ⌨️ (optional) Activity: Pull and run your docker image

If everything went correctly, we should now we able to see your image, pull it, run it and play the Stackoverflown game!

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
> Throughout this exercise, you will publish different versions of the image. You can always use these same steps to pull and run any version you create, even if not explicitly instructed.

### ⌨️ Activity: Enhance workflow with docker actions

Let's edit the workflow to use the official Docker actions for a more robust and feature-rich build process.

1. Open the `.github/workflows/docker-publish.yml` file.
1. Remove your existing `Build and push Docker image` step with `docker` commands. We will replace that with open source actions.
1. Add these following three steps in place of the step you just removed

   ```yaml
   - name: Set up QEMU
     uses: docker/setup-qemu-action@v3
   - name: Set up Docker Buildx
     uses: docker/setup-buildx-action@v3
   - name: Build and push Docker image
     uses: docker/build-push-action@v6
     with:
       context: .
       push: true
       platforms: linux/amd64,linux/arm64
       provenance: true
       tags: |
         ghcr.io/{{ full_repo_name | lower }}/stackoverflown:latest
         ghcr.io/{{ full_repo_name | lower }}/stackoverflown:{% raw %}${{ github.sha }}{% endraw %}
   ```

   Ensure the yaml indentation is setup correctly!

   > 💡 **Tip:** You can run `actionlint` command in the terminal to see if the workflow is properly formatted.

   <details>
   <summary>Having trouble? 🤷 See full workflow file</summary><br/>

   <!-- TODO -->

   </details>

1. Commit and push your changes to the `main` branch.
1. Monitor your workflow run in the [Actions](https://github.com/{{ full_repo_name }}/actions) tab of your repository.
