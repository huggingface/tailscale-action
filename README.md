# tailscale-action
Hugging Face Github action to connect to tailscale (Based from https://github.com/tailscale/github-action)

You can use this github action for 2 differents use cases :

# Access to internal shared ressources (like registry)

Ask for the TAILSCALE_AUTHKEY secret and add this step to your workflow. 

```yaml
  - name: Tailscale
    uses: huggingface/tailscale-action@main
    with:
      authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
```



# Log in SSH on the runner in order to debug the workflow

- Add this step to your workflow (`TAILSCALE_SSH_AUTHKEY`, `SLACK_CIFEEDBACK_CHANNEL`, `SLACK_CIFEEDBACK_BOT_TOKEN` already available on all repos)
- Re-Run your Job with `Enable debug logging` on the github popup
- Join the slack channel #github-runners, you will receive a slack message.

**WARNING : if you have a workflow with a lot of jobs, don't run your workflow with `Enable debug logging`**

```yaml
  - name: Tailscale
    uses: huggingface/tailscale-action@main
    with:
      authkey: ${{ secrets.TAILSCALE_SSH_AUTHKEY }}
       debugEnabled: ${{ secrets.ACTIONS_STEP_DEBUG }}
       slackChannel: ${{ secrets.SLACK_CIFEEDBACK_CHANNEL }}
       slackToken: ${{ secrets.SLACK_CIFEEDBACK_BOT_TOKEN }}
```


#€ Others options
`containerMode` is by default to True, because on Kubernetes, runners don't have access to TUN interface
`debugEnabled` is provided by secrets.ACTIONS_STEP_DEBUG [Enabling debug logging feature](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/enabling-debug-logging)
`slackChannel` and `slackToken` are used to send SSH informations on slack channel
`debug` is used to print out the WAN IP of tailscale tunnel
`version` is used to overwrite the default version use in this action. You will need to provide `sha256sum` as well.