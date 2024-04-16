# tailscale-action
Hugging Face Github action to connect to tailscale (Based from https://github.com/tailscale/github-action)

You can use this github action for 2 differents use cases :
- Access to internal shared ressources (like registry)
- Log in SSH on the runner in order to debug the workflow
In the first case, you can also configure Tailscale to automatically start SSH server in case of job failure

# Access to internal shared ressources (like registry)

Ask for the TAILSCALE_AUTHKEY secret and add this step to your workflow. 

```yaml
  - name: Tailscale
    uses: huggingface/tailscale-action@main
    with:
      authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
```

You can configure This Tailscale Action to run dynamically an SSH server on your runner if a step failed, or if you started your job with debug mode.
In this case, you have to add 2 inputs for slack notification and add a "wait" step at the end of your job

```yaml
  - name: Tailscale
    uses: huggingface/tailscale-action@main
    with:
      authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
      slackChannel: ${{ secrets.SLACK_CIFEEDBACK_CHANNEL }}
      slackToken: ${{ secrets.SLACK_CIFEEDBACK_BOT_TOKEN }}
```

```yaml
  - name: Tailscale Wait
    if: ${{ failure() || runner.debug == '1' }}
    uses: huggingface/tailscale-action@main
    with:
       waitForSSH: true
```

# Log in SSH on the runner in order to debug the workflow


- Add this step at the end of your job (`TAILSCALE_SSH_AUTHKEY`, `SLACK_CIFEEDBACK_CHANNEL`, `SLACK_CIFEEDBACK_BOT_TOKEN` already available on all repos)
- Re-Run your Job with `Enable debug logging` on the github popup
- Join the slack channel #github-runners, you will receive a slack message.

```yaml
  - name: Tailscale Wait
    if: ${{ failure() || runner.debug == '1' }}
    uses: huggingface/tailscale-action@main
    with:
       waitForSSH: true
       authkey: ${{ secrets.TAILSCALE_SSH_AUTHKEY }}
       slackChannel: ${{ secrets.SLACK_CIFEEDBACK_CHANNEL }}
       slackToken: ${{ secrets.SLACK_CIFEEDBACK_BOT_TOKEN }}
```

**WARNING : if you have a workflow with a lot of jobs, don't run your workflow with `Enable debug logging`**

Tooltip : If you want to connect to the runner at the start of your workflow, to be able to debug during steps 
- add the Tailscale action Step at the start of your job
- Add the Tailscale Wait action at the end

```yaml
  - name: Tailscale
          uses: huggingface/tailscale-action@main
          with:
            authkey: ${{ secrets.TAILSCALE_SSH_AUTHKEY }}
            slackChannel: ${{ secrets.SLACK_CIFEEDBACK_CHANNEL }}
            slackToken: ${{ secrets.SLACK_CIFEEDBACK_BOT_TOKEN }}
  .....

  - name: Tailscale Wait
    if: ${{ failure() || runner.debug == '1' }}
    uses: huggingface/tailscale-action@main
    with:
       waitForSSH: true
```

## Others options

- `sshTimeout` : by default Tailscale is waiting 5 minutes before terminating the job. You can increase this time.
