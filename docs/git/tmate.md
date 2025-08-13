# Tmate

[tmate](https://github.com/mxschmitt/action-tmate) is a tool that lets you connect to a running GitHub Actions runner
via SSH, allowing real-time interactive debugging.

## What is tmate?

- **Remote Terminal Access:** Opens a secure SSH session into the CI environment.
- **Runs in GitHub Actions:** No local installation required.
- **Interactive Debugging:** Lets you explore the runner’s file system, run commands, and investigate issues live.

## Why use tmate?

- Inspect the actual environment where your workflow runs.
- Reproduce issues interactively instead of guessing.
- Check environment variables, installed tools, or file contents.
- Debug complex CI problems more efficiently.

## How to Enable tmate in GitHub Actions

Add this step anywhere in your workflow where you want to pause and debug:

```yaml
- name: Setup tmate session
  uses: mxschmitt/action-tmate@v3
```

## Connecting to the Runner

1. Run the workflow.
2. When it reaches the `Setup tmate session` step, GitHub Actions logs will display:
    - An **SSH command** to connect from your terminal.
    - A **Web URL** to connect via browser terminal.
3. Use the SSH command in your terminal:
   ```bash
   ssh <connection_string> # ssh AVmfLLaAuYmMAhg3JX6YUWkBE@nyc1.tmate.io
   ```
4. Once connected, you have full terminal access to the runner.

## Ending the Session

- Type `exit` in the SSH terminal to close the session.
- The workflow will then continue to the next step.

## Notes

- The SSH session is temporary and only exists while the workflow is paused at the tmate step.
- Make sure sensitive data is handled carefully — anyone with the SSH link can access the runner.

## References

- [tmate GitHub Action](https://github.com/mxschmitt/action-tmate)
- [tmate Official Website](https://tmate.io/)
- [Debugging with tmate - GitHub Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/debugging-with-tmate)
