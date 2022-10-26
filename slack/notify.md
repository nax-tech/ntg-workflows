## About

A Github Action Workflow to send templated notifications to Slack. This workflow uses the [slack-send](https://github.com/marketplace/actions/slack-send) action to send a notification to one or more Slack channels. See [Technique 2](https://github.com/marketplace/actions/slack-send#technique-2-slack-app) to configure an appropriate Slack app and bot token.

## Usage

```yaml
jobs:
  notify:
    uses: nax-tech/ntg-workflows/slack/notify.yaml@v1
    needs: [test]
    with:
      project: My Amazing Project (Hello World!)
      message: |
        ${{ needs.test.result }} Test
      channel-ids: CDYR538CB6G, CI6D45JC3SW
    secrets:
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_TOKEN }}

```

## Notification Template

Our notification template can be broken down into 4 distinct parts in the templated notification: the title, CI metadata, an optional custom message, and a button that links to the calling workflow — i.e. the workflow that called this one.

### Title

The title of the notification indicates what actor initiated the workflow. By default, this is the name of the repository, but it can be customized by specifying a value to the `project` input.

### Metadata

Some CI metadata that may be useful to identify this workflow. These are displayed in a two-column view before the message (if any). There are four pieces of metadata included:

- **Workflow**: The name or filename of the calling workflow.
- **Branch**: The branch that a commit was made to — in the case of PRs this is the head branch not the base branch.
- **Run Number**: The unique run ID generated by Github Actions for this workflow run. This will be the same if the workflow is rerun.
- **Run Attempt**: The number of times this workflow has been run or rerun. The initial run of the workflow is considered attempt number 1 and all reruns will be assigned the next whole number in ascending order.

### Message

A user-defined custom message. If none is specified, no custom message will be added to the notification.

Before the message is included in the notification, a bash script is executed to convert certain words into standardized Slack emojis. These mappings between keywords and emojis is defined by the possible values of the [`needs.<job_id>.result` property](https://docs.github.com/en/actions/learn-github-actions/contexts#needs-context).

- `success` will be converted into ![:heavy_check_mark:](slack_success.png)
- `failure` will be converted into ![:x:](slack_failure.png)
- `cancelled` will be converted into ![:question:](slack_cancelled.png)
- `skipped` will be converted into ![:black_right_pointing_double_triangle_with_vertical_bar:](slack_skipped.png)

### Workflow Button

At the bottom of the notification, there will be a button that links to the calling workflow. While it might make sense to include a link to the specific job that failed — in the event that a job did fail — Github Actions, unfortunately, provides no way to get this URL from within a workflow.

## Inputs

| name | required | description |
| --- | --- | --- |
| `project` | false | The name of the project that should be included in the notification title. (Default: repository name) |
| `message` | false | A custom message to include in the notification. Parts of this message may be [converted into emojis](#custom-message) |
| `channel-ids` | true | A comma-separated list of the Slack Channel IDs where the notification should be sent. |

## Secrets

| name | required | description |
| --- | --- | --- |
| `SLACK_BOT_TOKEN` | true | The bot token used to authenticate to Slack. Usually begins with `xoxb`. |

## Outputs

None