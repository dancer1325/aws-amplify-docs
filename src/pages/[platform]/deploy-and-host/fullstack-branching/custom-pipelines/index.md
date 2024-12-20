* goal
  * set up fullstack CI/CD | custom pipeline (_Example:_ AWS CodePipeline, Amazon CodeCatalyst, GitHub Actions) 
  
* Amplify CI/CD
  * allows
    * zero-config setup,
    * fullstack previews,
    * centralized secrets management

## Set up backend deployments

* TODO:
You can set up your backend deployments using the following steps:

1.  Create an Amplify app by connecting a fullstack Gen 2 branch from your Git repository. This is a one time setup as for subsequent deployments, we will be using a custom pipeline.

2.  Disable Auto-build for your branch. This will ensure code commits to your branch will not trigger a build.

<Video src="/images/gen2/fullstack-branching/auto-build.mp4"  description="Video - Disable auto-build" />

3. Update the Amplify build specification file to add `npx ampx generate outputs --branch $AWS_BRANCH --app-id $AWS_APP_ID` and comment out the `pipeline-deploy` script. `ampx pipeline-deploy` runs a script to deploy backend updates, while `ampx generate outputs` fetches the latest `amplify_outputs.json` for the specified environment.

![custom-ci](/images/gen2/fullstack-branching/custom-ci.png)

4. Now go to your pipeline provider and update the build settings to include the following:
    * Run `npm ci`.
    * Run `export CI=1` to tell the deployment script that is a CI environment.
    * Run `npx ampx pipeline-deploy --branch BRANCH_NAME --app-id AMPLIFY_APP_ID`. `BRANCH_NAME` refers to the branch you're deploying. `AMPLIFY_APP_ID` is the Amplify App ID. To locate the `App ID` for your backend application, navigate to the Amplify console and select your **backend-app**. On the Overview page, the `App ID` is displayed under the project name.

The example below demonstrates how you would set up the build-spec when using Amazon CodeCatalyst.

```yaml
Actions:
  Build_82:
    # Identifies the action. Do not modify this value.
    Identifier: aws/build@v1.0.0
    # Specifies the source and/or artifacts to pass to the action as input.
    Inputs:
      # Optional
      Sources:
        - WorkflowSource # This specifies that the action requires this Workflow as a source
      Variables:
        - Name: BRANCH_NAME
          Value: main
        - Name: AMPLIFY_APP_ID
          Value: #####
    Configuration:
      # Required - Steps are sequential instructions that run shell commands
      Steps:
        - Run: export CI=1
        - Run: npm ci
        - Run: npx ampx pipeline-deploy --branch $BRANCH_NAME --app-id $AMPLIFY_APP_ID
```
5. Trigger a `git push` to your branch. Your build logs should show that there is an AWS CloudFormation deployment underway.

## Set up frontend deployments

If you want to complete the fullstack CI/CD setup, we have to build, deploy, and host the frontend in addition to the backend.

1. Use the Amplify Console to create an [incoming webhook](https://docs.aws.amazon.com/amplify/latest/userguide/webhooks.html).

2. Navigate to the frontend app, under **Hosting > Build settings** select **Create webhook**. Provide a **name** for the webhook and select the **target branch** to build on incoming webhook requests.

![Screenshot of the Build settings page in the Amplify console showing the incoming webhooks feature](/images/gen2/fullstack-branching/multirepo5.png)

3. Next, select the webhook and copy the `curl` command which will be used to trigger a build for the frontend app.

![Screenshot of the Incoming webhooks page in the Amplify console displaying the newly created webhook](/images/gen2/fullstack-branching/multirepo6.png)

4. Now update your custom-pipeline build settings to include the `curl` command to trigger a frontend build after the `pipeline-deploy` succeeds. Using the same Amazon CodeCatalyst example above, this step includes:

``` yaml
    Configuration:
      # Required - Steps are sequential instructions that run shell commands
      Steps:
        - Run: export CI=1
        - Run: npm ci
        - Run: npx ampx pipeline-deploy --branch $BRANCH_NAME --app-id $AMPLIFY_APP_ID
        - Run: if [ $BRANCH_NAME = "main" ]; then curl -X POST -d {}
            "https://webhooks.amplify.us-west-2.amazonaws.com/prod/webhooks?id=WEBHOOK-ID&token=TOKEN&operation=startbuild"
            -H "Content-Type:application/json"; fi
```

4. This should trigger a build in your Amplify app. Amplify CI will build and first generate the `amplify_outputs.json` for the branch and then build, deploy, and host the frontend.
