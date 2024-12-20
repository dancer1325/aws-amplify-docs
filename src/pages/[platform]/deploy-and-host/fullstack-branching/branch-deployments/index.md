* goal
  * fullstack branch deployments
    * allows
      * from feature branches -- automatically -- deploy changes about
        * infrastructure 
        * application code
    * uses
      * 👀from feature branches -- test -- changes 👀
        * previous to merge to main branch

## Set up feature branch deployments

* TODO:
After you've deployed your [first branch](/[platform]/start/quickstart/), you can manually connect more, but the recommended workflow is to use the **branch auto-detection** feature.

1. Log in to the [Amplify console](https://console.aws.amazon.com/amplify/home) and choose your app.

2. Navigate to **App settings > Branch settings**, select **Edit** and enable **Branch auto-detection** and **Branch auto-disconnection**. The following video uses the default settings, which will connect any branch in your repo automatically. Branch auto-disconnection will ensure that if you delete a branch from your repository, the branch will also be deleted.

<Callout>
You can also define a pattern to connect only certain branches. For example, setting `dev`, `staging`, and `feature/*` will automatically connect all three branch types. Your `dev` and `staging` branches, as well as any branch that begins with `feature/`, will be connected.
</Callout>

<Video src="/images/gen2/fullstack-branching/Enable-branch-autodetection.mp4" description="Video - Enable branch autodetection" />

3. Push a commit to your `feature/A` and `staging` branches that match the pattern. You should start seeing deployments on the console page. You will now have three fullstack branches deployed.

![Production, feature/A, and staging branches listed on app overview page in Amplify console.](/images/gen2/fullstack-branching/branches.png)

## Promote changes to production

In Gen 2, promoting changes to production follows the normal Git-based workflow.

![Workflow for merging changes from feature/A branch to main, or production, branch.](/images/gen2/fullstack-branching/gitflow.png)

1. Make a change in your `feature/A` branch.

```bash title="Terminal" showLineNumbers={false}
git checkout -b feature/A

## make some edits to your code

git commit --am "New data model to track comments for todos added"

git push origin feature/A
```

2. Submit a pull request to your `main` branch. Once your team has validated the changes, merge the pull request to `main`. This will initiate a build on your `main` branch and update any frontend or backend resources that you changed.


### Generate client config

You can generate the config for a branch environment by running:

<InlineFilter filters={['angular','javascript','nextjs','react','react-native','swift','vue']}>
For Web and React Native, generating the config with the default format and output directory.

```bash title="Terminal" showLineNumbers={false}
npx ampx generate outputs --app-id <your-amplify-app-id> --branch <your-git-branch-name> --out-dir <path/to/config>
```
</InlineFilter>

<InlineFilter filters={['android']}>
<Callout warning>
  Be sure to add a "raw" folder under app/src/main/res directory if it doesn't
  exist.
</Callout>

```bash title="Terminal" showLineNumbers={false}
npx ampx generate outputs --app-id <your-amplify-app-id> --branch <your-git-branch-name> --out-dir app/src/main/res/raw
```
</InlineFilter>
<InlineFilter filters={['flutter']}>
```bash title="Terminal" showLineNumbers={false}
npx ampx generate outputs --app-id <your-amplify-app-id> --branch <your-git-branch-name> --format dart --out-dir lib
```
</InlineFilter>
<InlineFilter filters={['swift']}>
```bash title="Terminal" showLineNumbers={false}
npx ampx generate outputs --app-id <your-amplify-app-id> --branch <your-git-branch-name>
```

Once the sandbox environment is running, you would also generate the configuration file for your application. However, Xcode won't recognize the file by default. To recognize the files, you need to drag and drop the generated configuration file to your project.

<Video src="/images/gen2/getting-started/ios/ios-getting-started-2.mp4" description="Video - iOS Getting Started" />

</InlineFilter>
