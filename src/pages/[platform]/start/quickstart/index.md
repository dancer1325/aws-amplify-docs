* goal
  * get started with AWS Amplify Gen 2 | ANY `platforms`

platforms = [
        'android',
        'angular',
        'flutter',
        'javascript',
        'nextjs',
        'react',
        'react-native',
        'swift',
        'vue'
      ]

# | "javascript"

* goal
  1. Deploy a Vanilla JavaScript app -- via -- Vite
  2. Build and connect -- to a -- database / real-time data updates
  3. Configure authentication and authorization rules

* see [amplify-js-app](./amplify-js-app/README.md)

# | "react"

* goal
  1. Deploy a React and Vite app
  2. Build and connect to a database with real-time data updates
  3. Configure authentication and authorization rules

* TODO: create specific project for it

## Deploy a fullstack app to AWS

We've created a starter "To-do" application to help get started faster. First, you will create a repository in your GitHub account using our starter React template.

### 1. Create the repository

Use our starter template to create a repository in your GitHub account. This template scaffolds `create-vite-app` with Amplify backend capabilities.

<ExternalLinkButton
  size="medium"
  href='https://github.com/new?template_name=amplify-vite-react-template&template_owner=aws-samples&name=amplify-vite-react-template&description=My%20Amplify%20Gen%202%20starter%20application'
>
<IconGithub />
Create repository from template
</ExternalLinkButton>

Use the form in GitHub to finalize your repo's creation.

### 2. Deploy the starter app

Now that the repository has been created, deploy it with Amplify.

<ExternalLinkButton
  size="medium"
  variation="primary"
  href='https://console.aws.amazon.com/amplify/create/repo-branch'
>
<IconAmplify />
Deploy to AWS
</ExternalLinkButton>

Select **GitHub**. After you give Amplify access to your GitHub account via the popup window, pick the repository and `main` branch to deploy. Make no other changes and click through the flow to **Save and deploy**.

<Video src="/images/gen2/getting-started/react/deploy.mp4" description="Video - Deploy the starter app" />

### 3. View deployed app

<Accordion title='Learn about the project structure' headingLevel='4' eyebrow='While you are waiting for your app to deploy (~5 mins)'>

Let's take a tour of the project structure in this starter repository by opening it on GitHub. The starter application has pre-written code for a to-do list app. It gives you a real-time database with a feed of all to-do list items and the ability to add new items.

```text
├── amplify/ # Folder containing your Amplify backend configuration
│   ├── auth/ # Definition for your auth backend
│   │   └── resource.tsx
│   ├── data/ # Definition for your data backend
│   │   └── resource.ts
|   ├── backend.ts
│   └── tsconfig.json
├── src/ # React UI code
│   ├── App.tsx # UI code to sync todos in real-time
│   ├── index.css # Styling for your app
│   └── main.tsx # Entrypoint of the Amplify client library
├── package.json
└── tsconfig.json
```
</Accordion>

 When the build completes, visit the newly deployed branch by selecting "Visit deployed URL". Since the build deployed an API, database, and authentication backend, you will be able to create new to-do items.

<Video src="/images/gen2/getting-started/react/hosted-app.mp4" description="Video - Visit Deployed URL" />

In the Amplify console, click into the deployment branch (in this case **main**) > select **Data** in the left-hand menu > **Data manager** to see the data entered in your database.

<Video src="/images/gen2/getting-started/amplify-console-data-manager.mp4" description="Video - Data Manager" />

## Make frontend updates

Let's learn how to enhance the app functionality by creating a delete flow for to-do list items.

### 4. Set up local environment

Now let's set up our local development environment to add features to the frontend. Click on your deployed branch and you will land on the **Deployments** page which shows you your build history and a list of deployed backend resources.

<Video src="/images/gen2/getting-started/react/branch-details.mp4" description="Video - Deployments" />

At the bottom of the page you will see a tab for **Deployed backend resources**. Click on the tab and then click the **Download amplify_outputs.json file** button.

![](/images/gen2/getting-started/react/amplify-outputs-download.png)

Clone the repository locally.

```bash title="Terminal" showLineNumbers={false}
git clone https://github.com/<github-user>/amplify-vite-react-template.git
cd amplify-vite-react-template && npm install
```

Now move the `amplify_outputs.json` file you downloaded above to the root of your project.

```text
├── amplify
├── src
├── amplify_outputs.json <== backend outputs file
├── package.json
└── tsconfig.json
```

<Accordion title='amplify_outputs.json' headingLevel='4' eyebrow='Learn more'>
The **amplify_outputs.json** file contains backend endpoint information, publicly-viewable API keys, authentication flow information, and more. The Amplify client library uses this outputs file to connect to your Amplify Backend.  You can review how the outputs file is imported within the `main.tsx` file and then passed into the `Amplify.configure(...)` function of the Amplify client library.
</Accordion>



### 5. Implement delete functionality

Go to the **src/App.tsx** file and add in a new `deleteTodo` functionality and pass function into the `<li>` element's `onClick` handler.

```tsx title="src/App.tsx"
function App() {
  // ...
  // highlight-start
  function deleteTodo(id: string) {
    client.models.Todo.delete({ id })
  }
  // highlight-end

  return (
    <main>
      <h1>My todos</h1>
      <button onClick={createTodo}>+ new</button>
      <ul>
        {todos.map(todo => <li
          // highlight-next-line
          onClick={() => deleteTodo(todo.id)}
          key={todo.id}>
          {todo.content}
        </li>)}
      </ul>
      <div>
        🥳 App successfully hosted. Try creating a new todo.
        <br />
        <a href="https://docs.amplify.aws/react/start/quickstart/">Review next step of this tutorial.</a>
      </div>
    </main>
  )
}
```

Try out the deletion functionality now by starting the local dev server:

```bash title="Terminal" showLineNumbers={false}
npm run dev
```

This should start a local dev server at http://localhost:5173.

<Video src="/images/gen2/getting-started/react/demo-delete.mp4" description="Video - Demo" />

### 6. Implement login UI

The starter application already has a pre-configured auth backend defined in the **amplify/auth/resource.ts** file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component. In your **src/main.tsx** file, import the Authenticator UI component and wrap your `<App>` component.

```tsx title="src/main.tsx"
import React from 'react';
import ReactDOM from 'react-dom/client';
// highlight-next-line
import { Authenticator } from '@aws-amplify/ui-react';
import { Amplify } from 'aws-amplify';
import App from './App.tsx';
import outputs from '../amplify_outputs.json';
import './index.css';
// highlight-next-line
import '@aws-amplify/ui-react/styles.css';

Amplify.configure(outputs);

ReactDOM.createRoot(document.getElementById('root')!).render(
  // highlight-start
  <React.StrictMode>
    <Authenticator>
      <App />
    </Authenticator>
  </React.StrictMode>
  // highlight-end
);
```

The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.

In your `src/App.tsx` file, add a button to enable users to sign out of the application. Import the [`useAuthenticator`](https://ui.docs.amplify.aws/react/connected-components/authenticator/advanced#access-auth-state) hook from the Amplify UI library to hook into the state of the Authenticator.

```tsx title="src/App.tsx"
import type { Schema } from '../amplify/data/resource';
// highlight-next-line
import { useAuthenticator } from '@aws-amplify/ui-react';
import { useEffect, useState } from 'react';
import { generateClient } from 'aws-amplify/data';

const client = generateClient<Schema>();

function App() {
  // highlight-next-line
  const { signOut } = useAuthenticator();

  // ...

  return (
    <main>
      {/* ... */}
      // highlight-next-line
      <button onClick={signOut}>Sign out</button>
    </main>
  );
}

export default App;
```

Try out your application in your localhost environment again. You should be presented with a login experience now.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added authenticator"
git push
```

Amplify automatically deploys the latest version of your app based on your git commits. In just a few minutes, when the application rebuilds, the hosted app will be updated to support the deletion functionality.

## Make backend updates

Let's update our backend to implement per-user authorization rules, allowing each user to only access their own to-dos.

### 7. Set up local AWS credentials

To make backend updates, we are going to require AWS credentials to deploy backend updates from our local machine.

**Skip ahead to step 8**, if you already have an AWS profile with credentials on your local machine, and your AWS profile has the `AmplifyBackendDeployFullAccess` permission policy.

Otherwise, **[set up local AWS credentials](/[platform]/start/account-setup/)** that grant Amplify permissions to deploy backend updates from your local machine.


### 8. Deploy cloud sandbox

To update your backend without affecting the production branch, use Amplify's cloud sandbox. This feature provides a separate backend environment for each developer on a team, ideal for local development and testing.

To start your cloud sandbox, run the following command in a **new Terminal window**:

```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox
```

Once the cloud sandbox has been fully deployed (~5 min), you'll see the `amplify_outputs.json` file updated with connection information to a new isolated authentication and data backend.

<Callout info>

The `npx ampx sandbox` command should run concurrently to your `npm run dev`. You can think of the cloud sandbox as the "localhost-equivalent for your app backend".

</Callout>

### 9. Implement per-user authorization

The to-do items in the starter are currently shared across all users, but, in most cases, you want data to be isolated on a per-user basis.

To isolate the data on a per-user basis, you can use an "owner-based authorization rule". Let's apply the owner-based authorization rule to your to-do items:

```ts title="amplify/data/resource.ts"
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a.model({
    content: a.string(),
    // highlight-next-line
  }).authorization(allow => [allow.owner()]),
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    // This tells the data client in your app (generateClient())
    // to sign API requests with the user authentication token.
    // highlight-next-line
    defaultAuthorizationMode: 'userPool',
  },
});
```

In the application client code, let's also render the username to distinguish different users once they're logged in. Go to your **src/App.tsx** file and render the `user` property from the `useAuthenticator` hook.

```tsx title="src/App.tsx"
// ... imports

function App() {
  // highlight-next-line
  const { user, signOut } = useAuthenticator();

  // ...

  return (
    <main>
      // highlight-next-line
      <h1>{user?.signInDetails?.loginId}'s todos</h1>
      {/* ... */}
    </main>
  )
}
```

Now, let's go back to your local application and test out the user isolation of the to-do items.

You will need to sign up new users again because now you're working with the cloud sandbox instead of your production backend.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added per-user data isolation"
git push
```

Once your build completes in the Amplify Console, the `main` backend will update to support the changes made within the cloud sandbox. The data in the cloud sandbox is fully isolated and won't pollute your production database.

# | "nextjs"


* goal
  1. Deploy a Next.js app
  2. Build and connect to a database with real-time data updates
  3. Configure authentication and authorization rules

We have two Quickstart guides you can follow:

<Overview childPageNodes={props.childPageNodes} />

# | "vue"

* goal
  1. Deploy a Vue.js app
  2. Build and connect to a database with real-time data updates
  3. Configure authentication and authorization rules

## Deploy a fullstack app to AWS

We've created a starter "To-do" application to help get started faster. First, you will create a repository in your GitHub account using our starter Vue template.

### 1. Create the repository

Use our starter template to create a repository in your GitHub account. This template scaffolds `create-vite-app` with Amplify backend capabilities.

<ExternalLinkButton
  size="medium"
  href='https://github.com/new?template_name=amplify-vue-template&template_owner=aws-samples&name=amplify-vue-template&description=My%20Amplify%20Gen%202%20starter%20application'
>
<IconGithub />
Create repository from template
</ExternalLinkButton>

Use the form in GitHub to finalize your repo's creation.

### 2. Deploy the starter app

Now that the repository has been created, deploy it with Amplify.

<ExternalLinkButton
  size="medium"
  variation="primary"
  href='https://console.aws.amazon.com/amplify/create/repo-branch'
>
<IconAmplify />
Deploy to AWS
</ExternalLinkButton>

Select **GitHub**. After you give Amplify access to your GitHub account via the popup window, pick the repository and `main` branch to deploy. Make no other changes and click through the flow to **Save and deploy**.

<Video src="/images/gen2/getting-started/react/deploy.mp4" description="Video - Deploy the starter app" />

### 3. View deployed app

<Accordion title='Learn about the project structure' headingLevel='4' eyebrow='While you are waiting for your app to deploy (~5 mins)'>

Let's take a tour of the project structure in this starter repository by opening it on GitHub. The starter application has pre-written code for a to-do list app. It gives you a real-time database with a feed of all to-do list items and the ability to add new items.

```text
├── amplify/ # Folder containing your Amplify backend configuration
│   ├── auth/ # Definition for your auth backend
│   │   └── resource.tsx
│   ├── data/ # Definition for your data backend
│   │   └── resource.ts
|   ├── backend.ts
│   └── tsconfig.json
├── src/ # Vue code
│   ├── assets/ # Styling for your app
│   ├── components/ # UI code to sync todos in real-time
│   ├── App.vue # UI layout
│   └── main.tsx # Entrypoint of the Amplify client library
├── package.json
└── tsconfig.json
```
</Accordion>

 When the build completes, visit the newly deployed branch by selecting "Visit deployed URL". Since the build deployed an API, database, and authentication backend, you will be able to create new to-do items.

<Video src="/images/gen2/getting-started/react/hosted-app.mp4" description="Video - Visit Deployed URL" />

In the Amplify console, click into the deployment branch (in this case **main**) > select **Data** in the left-hand menu > **Data manager** to see the data entered in your database.

<Video src="/images/gen2/getting-started/amplify-console-data-manager.mp4" description="Video - Data Manager" />

## Make frontend updates

Let's learn how to enhance the app functionality by creating a delete flow for to-do list items.

### 4. Set up local environment

Now let's set up our local development environment to add features to the frontend. Click on your deployed branch and you will land on the **Deployments** page which shows you your build history and a list of deployed backend resources.

<Video src="/images/gen2/getting-started/react/branch-details.mp4" description="Video - Deployments" />

At the bottom of the page you will see a tab for **Deployed backend resources**. Click on the tab and then click the **Download amplify_outputs.json file** button.

![](/images/gen2/getting-started/react/amplify-outputs-download.png)

Clone the repository locally.

```bash title="Terminal" showLineNumbers={false}
git clone https://github.com/<github-user>/amplify-vue-template.git
cd amplify-vue-template && npm install
```

Now move the `amplify_outputs.json` file you downloaded above to the root of your project.

```text
├── amplify
├── src
├── amplify_outputs.json <== backend outputs file
├── package.json
└── tsconfig.json
```

<Accordion title='amplify_outputs.json' headingLevel='4' eyebrow='Learn more'>
The **amplify_outputs.json** file contains backend endpoint information, publicly-viewable API keys, authentication flow information, and more. The Amplify client library uses this outputs file to connect to your Amplify Backend.  You can review how the outputs file is imported within the `main.tsx` file and then passed into the `Amplify.configure(...)` function of the Amplify client library.
</Accordion>



### 5. Implement delete functionality

Go to the **components/Todos.vue** file and add in a new `deleteTodo` functionality and pass function into the `<li>` element's `onClick` handler.

```tsx title="components/Todos.vue"
function App() {
  // ...
  // highlight-start
  function deleteTodo(id: string) {
    client.models.Todo.delete({ id })
  }
  // highlight-end

  <template>
  <main>
    <h1>My todos</h1>
    <button @click="createTodo">+ new</button>
    <ul>
      <li
        v-for="todo in todos"
        :key="todo.id"
        // highlight-start
        @click="deleteTodo(todo.id)"
        // highlight-end
      >
        {{ todo.content }}
      </li>
    </ul>
    <div>
      🥳 App successfully hosted. Try creating a new todo.
      <br />
      <a href="https://docs.amplify.aws/vue/start/quickstart/">
        Review next steps of this tutorial.
      </a>
    </div>
  </main>
</template>
}
```

Try out the deletion functionality now by starting the local dev server:

```bash title="Terminal" showLineNumbers={false}
npm run dev
```

This should start a local dev server at http://localhost:5173.

<Video src="/images/gen2/getting-started/react/demo-delete.mp4" description="Video - Demo" />

### 6. Implement login UI

The starter application already has a pre-configured auth backend defined in the **amplify/auth/resource.ts** file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component.

```terminal showLineNumbers={false}
npm add @aws-amplify/ui-vue
```
In your **src/App.vue** file, import the Authenticator UI component and wrap your `<main>` template.

```tsx title="src/App.vue"
<script>
// highlight-start
import { Authenticator } from "@aws-amplify/ui-vue";
import "@aws-amplify/ui-vue/styles.css";
// highlight-end
// ... other imports
</script>

<template>
  <main>
    // highlight-start
    <authenticator>
      <template v-slot="{ signOut }">
        <Todos />
        <button @click="signOut">Sign Out</button>
      </template>
    </authenticator>
    // highlight-end
  </main>
</template>
```

The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.

Try out your application in your localhost environment again. You should be presented with a login experience now.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added authenticator"
git push
```

Amplify automatically deploys the latest version of your app based on your git commits. In just a few minutes, when the application rebuilds, the hosted app will be updated to support the deletion functionality.

## Make backend updates

Let's update our backend to implement per-user authorization rules, allowing each user to only access their own to-dos.

### 7. Set up local AWS credentials

To make backend updates, we are going to require AWS credentials to deploy backend updates from our local machine.

**Skip ahead to step 8**, if you already have an AWS profile with credentials on your local machine, and your AWS profile has the `AmplifyBackendDeployFullAccess` permission policy.

Otherwise, **[set up local AWS credentials](/[platform]/start/account-setup/)** that grant Amplify permissions to deploy backend updates from your local machine.


### 8. Deploy cloud sandbox

To update your backend without affecting the production branch, use Amplify's cloud sandbox. This feature provides a separate backend environment for each developer on a team, ideal for local development and testing.

To start your cloud sandbox, run the following command in a **new Terminal window**:

```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox
```

Once the cloud sandbox has been fully deployed (~5 min), you'll see the `amplify_outputs.json` file updated with connection information to a new isolated authentication and data backend.

<Callout info>

The `npx ampx sandbox` command should run concurrently to your `npm run dev`. You can think of the cloud sandbox as the "localhost-equivalent for your app backend".

</Callout>

### 9. Implement per-user authorization

The to-do items in the starter are currently shared across all users, but, in most cases, you want data to be isolated on a per-user basis.

To isolate the data on a per-user basis, you can use an "owner-based authorization rule". Let's apply the owner-based authorization rule to your to-do items:

```ts title="amplify/data/resource.ts"
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a.model({
    content: a.string(),
    // highlight-next-line
  }).authorization(allow => [allow.owner()]),
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    // This tells the data client in your app (generateClient())
    // to sign API requests with the user authentication token.
    // highlight-next-line
    defaultAuthorizationMode: 'userPool',
  },
});
```

In the application client code, let's also render the username to distinguish different users once they're logged in.

```tsx title="src/App.vue"
<script>
// highlight-start
import { Authenticator } from "@aws-amplify/ui-vue";
import "@aws-amplify/ui-vue/styles.css";
// highlight-end
// ... other imports
</script>

<template>
  <main>
    <authenticator>
    // highlight-start
      <template v-slot="{ user, signOut }">
        <h1>Hello {{user?.signInDetails?.loginId}}'s todos</h1>
    // highlight-end
        <Todos />
        <button @click="signOut">Sign Out</button>
      </template>
    </authenticator>
  </main>
</template>
```

Now, let's go back to your local application and test out the user isolation of the to-do items.

You will need to sign up new users again because now you're working with the cloud sandbox instead of your production backend.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added per-user data isolation"
git push
```

Once your build completes in the Amplify Console, the `main` backend will update to support the changes made within the cloud sandbox. The data in the cloud sandbox is fully isolated and won't pollute your production database.

# | "angular"


* goal
  1. Deploy an Angular app
  2. Build and connect to a database with real-time data updates
  3. Configure authentication and authorization rules

## Deploy a fullstack app to AWS

We've created a starter "To-do" application to help get started faster. First, you will create a repository in your GitHub account using our starter Angular template.

### 1. Create the repository

Use our starter template to create a repository in your GitHub account. This template scaffolds a starter Angular application with Amplify backend capabilities.

<ExternalLinkButton
  size="medium"
  href='https://github.com/new?template_name=amplify-angular-template&template_owner=aws-samples&name=amplify-angular-template&description=My%20Amplify%20Gen%202%20starter%20application'
>
<IconGithub />
Create repository from template
</ExternalLinkButton>

Use the form in GitHub to finalize your repo's creation.

### 2. Deploy the starter app

Now that the repository has been created, deploy it with Amplify.

<ExternalLinkButton
  size="medium"
  variation="primary"
  href='https://console.aws.amazon.com/amplify/create/repo-branch'
>
<IconAmplify />
Deploy to AWS
</ExternalLinkButton>

Select **GitHub**. After you give Amplify access to your GitHub account via the popup window, pick the repository and `main` branch to deploy. Make no other changes and click through the flow to **Save and deploy**.

<Video src="/images/gen2/getting-started/react/deploy.mp4" description="Video - Deploy the starter app" />

### 3. View deployed app

<Accordion title='Learn about the project structure' headingLevel='4' eyebrow='While you are waiting for your app to deploy (~5 mins)'>

Let's take a tour of the project structure in this starter repository by opening it on GitHub. The starter application has pre-written code for a to-do list app. It gives you a real-time database with a feed of all to-do list items and the ability to add new items.

```text
├── amplify/ # Folder containing your Amplify backend configuration
│   ├── auth/ # Definition for your auth backend
│   │   └── resource.tsx
│   ├── data/ # Definition for your data backend
│   │   └── resource.ts
|   ├── backend.ts
│   └── tsconfig.json
├── src/app/ # Angular UI code
│   ├── todos/ # UI code to sync todos in real-time
│   ├── app.component.css # Styling for your app
│   └── app.component.ts # Entrypoint of the Amplify client library
├── package.json
└── tsconfig.json
```
</Accordion>

 When the build completes, visit the newly deployed branch by selecting "Visit deployed URL". Since the build deployed an API, database, and authentication backend, you will be able to create new to-do items.

<Video src="/images/gen2/getting-started/react/hosted-app.mp4" description="Video - Visit Deployed URL" />

In the Amplify console, click into the deployment branch (in this case **main**) > select **Data** in the left-hand menu > **Data manager** to see the data entered in your database.

<Video src="/images/gen2/getting-started/amplify-console-data-manager.mp4" description="Video - Data Manager"/>

## Make frontend updates

Let's learn how to enhance the app functionality by creating a delete flow for to-do list items.

### 4. Set up local environment

Now let's set up our local development environment to add features to the frontend. Click on your deployed branch and you will land on the **Deployments** page which shows you your build history and a list of deployed backend resources.

<Video src="/images/gen2/getting-started/react/branch-details.mp4" description="Video - Deployments" />

At the bottom of the page you will see a tab for **Deployed backend resources**. Click on the tab and then click the **Download amplify_outputs.json file** button.

![](/images/gen2/getting-started/react/amplify-outputs-download.png)

Clone the repository locally.

```bash title="Terminal" showLineNumbers={false}
git clone https://github.com/<github-user>/amplify-angular-template.git
cd amplify-angular-template && npm install
```

Now move the `amplify_outputs.json` file you downloaded above to the root of your project.

```text
├── amplify
├── src
├── amplify_outputs.json <== backend outputs file
├── package.json
└── tsconfig.json
```

<Accordion title='amplify_outputs.json' headingLevel='4' eyebrow='Learn more'>
The **amplify_outputs.json** file contains backend endpoint information, publicly-viewable API keys, authentication flow information, and more. The Amplify client library uses this outputs file to connect to your Amplify Backend.  You can review how the outputs file is imported within the `main.tsx` file and then passed into the `Amplify.configure(...)` function of the Amplify client library.
</Accordion>


### 5. Implement delete functionality

Go to the **src/app/todos/todos.component.ts** file and add a new `deleteTodo` function.

```tsx title="src/todos/todos.component.ts"
export class TodosComponent implements OnInit {
  // ...
  // highlight-start
  deleteTodo(id: string) {
    client.models.Todo.delete({ id })
  }
  // highlight-end
}
```

Call the `deleteTodo` function from the UI.

```html title="src/app/todos/todos.component.html"
...
  <ul>
    <li *ngFor="let todo of todos;" (click)="deleteTodo(todo.id)">
        {{ todo.content }}
    </li>
  </ul>
...
```

Try out the deletion functionality now by starting the local dev server:

```bash title="Terminal" showLineNumbers={false}
npm run start
```

This should start a local dev server at http://localhost:4200.

<Video src="/images/gen2/getting-started/react/demo-delete.mp4" description="Video - Demo" />

### 6. Implement login UI

The starter application already has a pre-configured auth backend defined in the **amplify/auth/resource.ts** file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component.

```terminal showLineNumbers={false}
npm add @aws-amplify/ui-angular
```

In your **src/app/app.component.ts** file, import the `AmplifyAuthenticatorModule`.

```ts title="src/app/app.component.ts"
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { TodosComponent } from './todos/todos.component';
import { Amplify } from 'aws-amplify';
import outputs from '../../amplify_outputs.json';
// highlight-next-line
import { AmplifyAuthenticatorModule, AuthenticatorService } from '@aws-amplify/ui-angular';

Amplify.configure(outputs);

@Component({
  selector: 'app-root',
  standalone: true,
  templateUrl: './app.component.html',
  styleUrl: './app.component.css',
  // highlight-next-line
  imports: [RouterOutlet, TodosComponent, AmplifyAuthenticatorModule],
})
export class AppComponent {
  title = 'amplify-angular-template';
  // highlight-start
  constructor(public authenticator: AuthenticatorService) {
    Amplify.configure(outputs);
  }
  // highlight-end
}
```
Update the application UI and include styles.

```html title="src/app/app.component.html"
<amplify-authenticator>
  <ng-template
    amplifySlot="authenticated"
    let-user="user"
    let-signOut="signOut"
  >
    <app-todos></app-todos>
    <button (click)="signOut()">Sign Out</button>
  </ng-template>
</amplify-authenticator>
```

```json title="angular.json"
...
    "styles": [
      "node_modules/@aws-amplify/ui-angular/theme.css",
      "src/styles.css"
    ],
...
```
The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.

Try out your application in your localhost environment again. You should be presented with a login experience now.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added authenticator"
git push
```

Amplify automatically deploys the latest version of your app based on your git commits. In just a few minutes, when the application rebuilds, the hosted app will be updated to support the deletion functionality.

## Make backend updates

Let's update our backend to implement per-user authorization rules, allowing each user to only access their own to-dos.

### 7. Set up local AWS credentials

To make backend updates, we are going to require AWS credentials to deploy backend updates from our local machine.

**Skip ahead to step 8**, if you already have an AWS profile with credentials on your local machine, and your AWS profile has the `AmplifyBackendDeployFullAccess` permission policy.

Otherwise, **[set up local AWS credentials](/[platform]/start/account-setup/)** that grant Amplify permissions to deploy backend updates from your local machine.


### 8. Deploy cloud sandbox

To update your backend without affecting the production branch, use Amplify's cloud sandbox. This feature provides a separate backend environment for each developer on a team, ideal for local development and testing.

To start your cloud sandbox, run the following command in a **new Terminal window**:

```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox
```

Once the cloud sandbox has been fully deployed (~5 min), you'll see the `amplify_outputs.json` file updated with connection information to a new isolated authentication and data backend.

<Callout info>

The `npx ampx sandbox` command should run concurrently to your `npm run dev`. You can think of the cloud sandbox as the "localhost-equivalent for your app backend".

</Callout>

### 9. Implement per-user authorization

The to-do items in the starter are currently shared across all users, but, in most cases, you want data to be isolated on a per-user basis.

To isolate the data on a per-user basis, you can use an "owner-based authorization rule". Let's apply the owner-based authorization rule to your to-do items:

```ts title="amplify/data/resource.ts"
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a.model({
    content: a.string(),
  })
  // highlight-next-line
  .authorization(allow => [allow.owner()]),
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    // This tells the data client in your app (generateClient())
    // to sign API requests with the user authentication token.
    // highlight-next-line
    defaultAuthorizationMode: 'userPool',
  },
});
```

In the application client code, let's also render the username to distinguish different users once they're logged in.

```html title="src/app/app.component.html"
<amplify-authenticator>
  <ng-template
    amplifySlot="authenticated"
    let-user="user"
    let-signOut="signOut"
  >
  <h1>Hello {{user?.signInDetails?.loginId}}'s todos</h1>
    <app-todos></app-todos>
    <button (click)="signOut()">Sign Out</button>
  </ng-template>
</amplify-authenticator>
```

Now, let's go back to your local application and test out the user isolation of the to-do items.

You will need to sign up new users again because now you're working with the cloud sandbox instead of your production backend.

<Video src="/images/gen2/getting-started/react/demo-auth.mp4" description="Video - Authentication Demo" />

To get these changes to the cloud, commit them to git and push the changes upstream.

```bash title="Terminal" showLineNumbers={false}
git commit -am "added per-user data isolation"
git push
```

Once your build completes in the Amplify Console, the `main` backend will update to support the changes made within the cloud sandbox. The data in the cloud sandbox is fully isolated and won't pollute your production database.

# | "flutter"


## Prerequisites

Before you get started, make sure you have the following installed:

- [Node.js](https://nodejs.org/) v18.17 or later
- [npm](https://www.npmjs.com/) v9 or later
- [git](https://git-scm.com/) v2.14.1 or later
- You will also need to [create an AWS Account](https://portal.aws.amazon.com/billing/signup). Note that AWS Amplify is part of the [AWS Free Tier](https://aws.amazon.com/amplify/pricing/).
- Configure your AWS account to use with Amplify [instructions](/[platform]/start/account-setup/).
- A stable version of [Flutter](https://docs.flutter.dev/get-started/install).

<Callout info>
You can follow the [official documentation](https://flutter.dev/docs/get-started/install) to install Flutter on your machine and check the [editor documentation](https://docs.flutter.dev/get-started/editor) for setting up your editor.
</Callout>

Once you have installed Flutter, you can create a new Flutter project using the following command:

<Callout info>
In this Quickstart guide, you will build the application for web. However, if you want to run the application on other platforms, be sure to follow the required setup [guide here](/[platform]/start/platform-setup/).
</Callout>

```bash title="Terminal" showLineNumbers={false}
flutter create my_amplify_app
```

## Create Backend

The easiest way to get started with AWS Amplify is through npm with `create-amplify` command. You can run it from your base project directory. First, go to the base project directory with the following command:

```bash title="Terminal" showLineNumbers={false}
cd my_amplify_app
```

After that, run the following to create an Amplify project:

```bash title="Terminal" showLineNumbers={false}
npm create amplify@latest -y
```

Running this command will scaffold Amplify backend files in your current project with the following files added:

```text
├── amplify/
│   ├── auth/
│   │   └── resource.ts
│   ├── data/
│   │   └── resource.ts
│   ├── backend.ts
│   └── package.json
├── node_modules/
├── .gitignore
├── package-lock.json
├── package.json
└── tsconfig.json
```

To deploy your backend use Amplify's per-developer cloud sandbox. This feature provides a separate backend environment for every developer on a team, ideal for local development and testing. To run your application with a sandbox environment, you can run the following command:


<InlineFilter filters={["android", "javascript", "react-native", "angular", "nextjs", "react", "react-native", "vue", "swift"]}>
```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox

```
</InlineFilter>

<InlineFilter filters={["flutter"]}>
```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox --outputs-format dart --outputs-out-dir lib
```

</InlineFilter>

## Adding Authentication

The initial scaffolding already has a pre-configured auth backend defined in the `amplify/auth/resource.ts` file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component available in the Amplify UI library.

To use the Authenticator, you need to add the following dependencies to your project:

```yaml title="pubspec.yaml"
dependencies:
  amplify_flutter: ^2.0.0
  amplify_auth_cognito: ^2.0.0
  amplify_authenticator: ^2.0.0
```

You will add:

- `amplify_flutter` to connect your application with the Amplify resources.
- `amplify_auth_cognito` to connect your application with the Amplify Cognito resources.
- `amplify_authenticator` to use the Amplify UI components.

After adding the dependencies, you need to run the following command to install the dependencies:

```bash title="Terminal" showLineNumbers={false}
flutter pub get
```

Lastly update your main.dart file to use the Amplify UI components:

```dart title="main.dart"
import 'package:amplify_auth_cognito/amplify_auth_cognito.dart';
import 'package:amplify_authenticator/amplify_authenticator.dart';
import 'package:amplify_flutter/amplify_flutter.dart';
import 'package:flutter/material.dart';

import 'amplify_outputs.dart';

Future<void> main() async {
  try {
    WidgetsFlutterBinding.ensureInitialized();
    await _configureAmplify();
    runApp(const MyApp());
  } on AmplifyException catch (e) {
    runApp(Text("Error configuring Amplify: ${e.message}"));
  }
}

Future<void> _configureAmplify() async {
  try {
    await Amplify.addPlugin(AmplifyAuthCognito());
    await Amplify.configure(amplifyConfig);
    safePrint('Successfully configured');
  } on Exception catch (e) {
    safePrint('Error configuring Amplify: $e');
  }
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return Authenticator(
      child: MaterialApp(
        builder: Authenticator.builder(),
        home: const Scaffold(
          body: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                SignOutButton(),
                Text('TODO Application'),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.

Run your application in your local environment again. You should be presented with a login experience now.


<Video src="/images/gen2/getting-started/flutter/flutter-getting-started-1.mp4" description="Video - Flutter Getting Started" />

## Adding Data

The initial scaffolding already has a pre-configured data backend defined in the `amplify/data/resource.ts` file. The default example will create a Todo model with `content` field.

Let's modify this to add the following:
- A boolean `isDone` field.
- An authorization rules specifying owners, authenticated via your Auth resource can "create", "read", "update", and "delete" their own records.
- Update the `defaultAuthorizationMode` to sign API requests with the user authentication token.

```typescript
import { type ClientSchema, a, defineData } from "@aws-amplify/backend";

const schema = a.schema({
  Todo: a
    .model({
      content: a.string(),
      isDone: a.boolean(),
    })
    .authorization(allow => [allow.owner()]),
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: "userPool",
  },
});
```
Next, let's implement UI to create, list, and delete the to-do items.

Amplify can automatically generate code for interacting with the backend API. Run the command in the terminal to generate dart model classes from the Data schema under `lib/models`:

```bash title="Terminal" showLineNumbers={false}
npx ampx generate graphql-client-code --format modelgen --model-target dart --out lib/models
```


Once you are done, add the API dependencies to your project. You will add `amplify_api` to connect your application with the Amplify API.

```yaml title="pubspec.yaml"
dependencies:
  amplify_api: ^2.0.0
```


After adding the dependencies, update the `_configureAmplify` method in your `main.dart` file to use the Amplify API:

```dart title="main.dart"
Future<void> _configureAmplify() async {
  try {
    await Amplify.addPlugins(
      [
        AmplifyAuthCognito(),
        AmplifyAPI(
          options: APIPluginOptions(
            modelProvider: ModelProvider.instance,
          ),
        ),
      ],
    );
    await Amplify.configure(amplifyConfig);
    safePrint('Successfully configured');
  } on Exception catch (e) {
    safePrint('Error configuring Amplify: $e');
  }
}
```

Next create a new widget called `TodoScreen` and add the following code to the end of the **main.dart** file:

```dart title="main.dart"

class TodoScreen extends StatefulWidget {
  const TodoScreen({super.key});

  @override
  State<TodoScreen> createState() => _TodoScreenState();
}

class _TodoScreenState extends State<TodoScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton.extended(
        label: const Text('Add Random Todo'),
        onPressed: () async {
          final newTodo = Todo(
            id: uuid(),
            content: "Random Todo ${DateTime.now().toIso8601String()}",
            isDone: false,
          );
          final request = ModelMutations.create(newTodo);
          final response = await Amplify.API.mutate(request: request).response;
          if (response.hasErrors) {
            safePrint('Creating Todo failed.');
          } else {
            safePrint('Creating Todo successful.');
          }
        },
      ),
      body: const Placeholder(),
    );
  }
}
```

This will create a random Todo every time a user clicks on the floating action button. You can see the `ModelMutations.create` method is used to create a new Todo.

And update the `MyApp` widget in your **main.dart** file like the following:

```dart title="main.dart"
class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return Authenticator(
      child: MaterialApp(
        builder: Authenticator.builder(),
        home: const SafeArea(
          child: Scaffold(
            body: Column(
              children: [
                SignOutButton(),
                Expanded(child: TodoScreen()),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

Next add a `_todos` list in `_TodoScreenState` to add the results from the API and call the refresh function:

```dart title="main.dart"
List<Todo> _todos = [];

@override
void initState() {
  super.initState();
  _refreshTodos();
}
```


and create a new function called `_refreshTodos`:

```dart title="main.dart"
Future<void> _refreshTodos() async {
  try {
    final request = ModelQueries.list(Todo.classType);
    final response = await Amplify.API.query(request: request).response;

    final todos = response.data?.items;
    if (response.hasErrors) {
      safePrint('errors: ${response.errors}');
      return;
    }
    setState(() {
      _todos = todos!.whereType<Todo>().toList();
    });
  } on ApiException catch (e) {
    safePrint('Query failed: $e');
  }
}
```

and update the `build` function like the following:

```dart title="main.dart"
@override
Widget build(BuildContext context) {
  return Scaffold(
    floatingActionButton: FloatingActionButton.extended(
      label: const Text('Add Random Todo'),
      onPressed: () async {
        final newTodo = Todo(
          id: uuid(),
          content: "Random Todo ${DateTime.now().toIso8601String()}",
          isDone: false,
        );
        final request = ModelMutations.create(newTodo);
        final response = await Amplify.API.mutate(request: request).response;
        if (response.hasErrors) {
          safePrint('Creating Todo failed.');
        } else {
          safePrint('Creating Todo successful.');
        }
        _refreshTodos();
      },
    ),
    body: _todos.isEmpty == true
        ? const Center(
            child: Text(
              "The list is empty.\nAdd some items by clicking the floating action button.",
              textAlign: TextAlign.center,
            ),
          )
        : ListView.builder(
            itemCount: _todos.length,
            itemBuilder: (context, index) {
              final todo = _todos[index];
              return Dismissible(
                key: UniqueKey(),
                confirmDismiss: (direction) async {
                  return false;
                },
                child: CheckboxListTile.adaptive(
                  value: todo.isDone,
                  title: Text(todo.content!),
                  onChanged: (isChecked) async {},
                ),
              );
            },
          ),
  );
}
```

Now let's add the update and delete functionality.

For update, add the following code to the `onChanged` method of the `CheckboxListTile.adaptive` widget:

```dart title="main.dart"
final request = ModelMutations.update(
  todo.copyWith(isDone: isChecked!),
);
final response =
    await Amplify.API.mutate(request: request).response;
if (response.hasErrors) {
  safePrint('Updating Todo failed. ${response.errors}');
} else {
  safePrint('Updating Todo successful.');
  await _refreshTodos();
}
```

This will call the `ModelMutations.update` method to update the Todo with a copied/updated version of the todo item. So now the checkbox will get an update as well.

For delete functionality, add the following code to the `confirmDismiss` method of the `Dismissible` widget:

```dart title="main.dart"
if (direction == DismissDirection.endToStart) {
  final request = ModelMutations.delete(todo);
  final response =
      await Amplify.API.mutate(request: request).response;
  if (response.hasErrors) {
    safePrint('Updating Todo failed. ${response.errors}');
  } else {
    safePrint('Updating Todo successful.');
    await _refreshTodos();
    return true;
  }
}
return false;
```

This will delete the Todo item when the user swipes the item from right to left. Now if you run the application you should see the following flow.

<Video src="/images/gen2/getting-started/flutter/flutter-getting-started-2.mp4" description="Video - Flutter Getting Started" />

You can terminate the sandbox environment now to clean up the project.

### Publishing changes to cloud

Publishing changes to the cloud requires a remote git repository. Amplify offers fullstack branch deployments that allow you to automatically deploy infrastructure and application code changes from feature branches. To learn more, visit the [fullstack branch deployments guide](/[platform]/deploy-and-host/fullstack-branching/branch-deployments).

# | "swift"

## Prerequisites

Before you get started, make sure you have the following installed:

- [Node.js](https://nodejs.org/) v18.17 or later
- [npm](https://www.npmjs.com/) v9 or later
- [git](https://git-scm.com/) v2.14.1 or later
- You will also need to [create an AWS Account](https://portal.aws.amazon.com/billing/signup). Note that AWS Amplify is part of the [AWS Free Tier](https://aws.amazon.com/amplify/pricing/).
- Configure your AWS account to use with Amplify [instructions](/[platform]/start/account-setup/).
- You need to have [Xcode and Developer Tooling](https://developer.apple.com/xcode/) installed on your machine.

<Accordion title='Create an XCode project' headingLevel='4' eyebrow='Starting fresh?'>
Open Xcode and select **Create New Project...**

![Shows the Xcode starter video to start project](/images/lib/getting-started/ios/set-up-swift-1.png)

In the next step select the **App** template under **iOS**. Click on next.

![Shows the template of apps for iOS](/images/lib/getting-started/ios/set-up-swift-2.png)

Next steps are:

- Adding a _Product Name_ (e.g. MyAmplifyApp)
- Select a _Team_ (e.g. None)
- Select a _Organization Identifier_ (e.g. com.example)
- Select **SwiftUI** an _Interface_.
- Press **Next**

![Shows the project details dialog](/images/lib/getting-started/ios/set-up-swift-3.png)

Now you should have your project created.

![Shows the base project for SwiftUI](/images/lib/getting-started/ios/set-up-swift-4.png)
</Accordion>

## Create Backend

The easiest way to get started with AWS Amplify is through npm with `create-amplify` command. You can run it from your base project directory.

```bash title="Terminal" showLineNumbers={false}
cd my_amplify_app
npm create amplify@latest
? Where should we create your project? (.) # press enter
```

Running this command will scaffold Amplify backend files in your current project with the following files added:

```text
├── amplify/
│   ├── auth/
│   │   └── resource.ts
│   ├── data/
│   │   └── resource.ts
│   ├── backend.ts
│   └── package.json
├── node_modules/
├── .gitignore
├── package-lock.json
├── package.json
└── tsconfig.json
```

To deploy your backend use Amplify's per-developer cloud sandbox. This feature provides a separate backend environment for every developer on a team, ideal for local development and testing. To run your application with a sandbox environment, you can run the following command:


```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox
```

Once the sandbox environment is deployed, it will create an `amplify_outputs.json`. However, Xcode won't be able to recognize them. For recognizing the files, you need to drag and drop the generated files to your project.

<Video src="/images/gen2/getting-started/ios/ios-getting-started-2.mp4" description="Video - XCode Demo" />

## Adding Authentication

The initial scaffolding already has a pre-configured auth backend defined in the `amplify/auth/resource`.ts file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component available in the Amplify UI library.

To use the Authenticator, open your project in Xcode and select **File > Add Packages...** and add the following dependencies:

![Shows the Amplify library for Swift](/images/lib/getting-started/ios/set-up-swift-5.png)

- Amplify Library for Swift: Enter its GitHub URL (https://github.com/aws-amplify/amplify-swift), select **Up to Next Major Version** and click **Add Package Dependencies...** and select the following libraries:

  - Amplify
  - AWSCognitoAuthPlugin

![Shows the Amplify library for Swift](/images/lib/getting-started/ios/set-up-swift-6.png)

- Amplify UI Swift - Authenticator: Enter its GitHub URL (https://github.com/aws-amplify/amplify-ui-swift-authenticator), select **Up to Next Major Version** and click **Add Package Dependencies...** and select the following libraries:
  - Authenticator

![Shows the Amplify library for Swift](/images/lib/getting-started/ios/set-up-swift-7.png)

Now update the `MyAmplifyAppApp` class with the following code:

```swift
import Amplify
import Authenticator
import AWSCognitoAuthPlugin
import SwiftUI

@main
struct MyApp: App {
    init() {
        do {
            try Amplify.add(plugin: AWSCognitoAuthPlugin())
            try Amplify.configure(with: .amplifyOutputs)
        } catch {
            print("Unable to configure Amplify \(error)")
        }
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

Update `ContentView` with the following code:
```swift
import Amplify
import Authenticator

struct ContentView: View {
    var body: some View {
        Authenticator { state in
            VStack {
                Button("Sign out") {
                    Task {
                        await state.signOut()
                    }
                }
            }
        }
    }
}
```

The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.

Run your application in your local environment again. You should be presented with a login experience now.

<Video with="40%" src="/images/gen2/getting-started/ios/ios-getting-started-1.mp4" description="Video - Authentication Demo" />

## Adding Data

The initial scaffolding already has a pre-configured data backend defined in the `amplify/data/resource.ts` file. The default example will create a Todo model with `content` field.

Let's modify this to add the following:
- A boolean `isDone` field.
- An authorization rules specifying owners, authenticated via your Auth resource can "create", "read", "update", and "delete" their own records.
- Update the `defaultAuthorizationMode` to sign API requests with the user authentication token.

```typescript
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a
    .model({
      content: a.string(),
      isDone: a.boolean().required()
    })
    .authorization((allow) => [allow.owner()])
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: 'userPool'
  }
});
```
Next, let's implement UI to create, list, and delete the to-do items.

Amplify can automatically generate code for interacting with the backend API. The command below generates model classes from the Data schema:

```bash title="Terminal" showLineNumbers={false}
npx ampx generate graphql-client-code --format modelgen --model-target swift
```

Move the generated files to your project. You can do this by dragging and dropping the files to your project.

![Shows the drag and drop phase](/images/lib/getting-started/ios/set-up-swift-8.png)

Once you are done, add the API dependencies to your project. Select **File > Add Package Dependencies...** and add the `AWSAPIPlugin`.

![Shows the Amplify API library for Swift selected](/images/lib/getting-started/ios/set-up-swift-9.png)

After adding the dependencies, update the `import` part of your `MyAmplifyAppApp.swift` file with the following code:

```swift title="MyAmplifyAppApp.swift"
import Amplify
import AWSCognitoAuthPlugin
import AWSAPIPlugin
```

Then, update the `init()` part of your `MyAmplifyAppApp.swift` file with the following code:

```swift title="MyAmplifyAppApp.swift"
init() {
    do {
        try Amplify.add(plugin: AWSCognitoAuthPlugin())
        try Amplify.add(plugin: AWSAPIPlugin(modelRegistration: AmplifyModels()))
        try Amplify.configure(with: .amplifyOutputs)
    } catch {
        print("Unable to configure Amplify \(error)")
    }
}
```

Create a new file called `TodoViewModel.swift` and the `createTodo` function with the following code:

```swift title="TodoViewModel.swift"
import Amplify
import SwiftUI

@MainActor
class TodoViewModel: ObservableObject {
    func createTodo() async {
        let creationTime = Temporal.DateTime.now()
        let todo = Todo(
            content: "Random Todo \(creationTime.iso8601String)",
            isDone: false,
            createdAt: creationTime,
            updatedAt: creationTime
        )
        do {
            let result = try await Amplify.API.mutate(request: .create(todo))
            switch result {
            case .success(let todo):
                print("Successfully created todo: \(todo)")
                todos.append(todo)
            case .failure(let error):
                print("Got failed result with \(error.errorDescription)")
            }
        } catch let error as APIError {
            print("Failed to create todo: ", error)
        } catch {
            print("Unexpected error: \(error)")
        }
    }
}

```

The code above will create a random todo with the current time.

Next, update the `listTodos` function in the `TodoViewModel.swift` for listing to-do items:

```swift title="TodoViewModel.swift"
@MainActor
class TodoViewModel: ObservableObject {
    @Published var todos: [Todo] = []

    func createTodo() {
        /// ...
    }

    func listTodos() async {
        let request = GraphQLRequest<Todo>.list(Todo.self)
        do {
            let result = try await Amplify.API.query(request: request)
            switch result {
            case .success(let todos):
                print("Successfully retrieved list of todos: \(todos)")
                self.todos = todos.elements
            case .failure(let error):
                print("Got failed result with \(error.errorDescription)")
            }
        } catch let error as APIError {
            print("Failed to query list of todos: ", error)
        } catch {
            print("Unexpected error: \(error)")
        }
    }
}
```

This will assign the value of the fetched todos into a Published object.

Now let's update the UI code to observe the todos. Update the `VStack` in the `ContentView.swift` file with the following code:

```swift title="ContentView.swift"
struct ContentView: View {

    // Create an observable object instance.
    @StateObject var vm = TodoViewModel()

    var body: some View {
        Authenticator { state in
            VStack {
                Button("Sign out") {
                    Task {
                        await state.signOut()
                    }
                }
                Button(action: {
                    Task { await vm.createTodo() }
                }) {
                    HStack {
                        Text("Add a New Todo")
                        Image(systemName: "plus")
                    }
                }
                .accessibilityLabel("New Todo")
            }
        }
    }
}
```

<Callout info>
  Throughout the Swift implementation, the async/await pattern has been used and
  for using it easily, we take advantage of the Task structure. For more
  information about the Task structure, you can check the
  [documentation](https://developer.apple.com/documentation/swift/task).
</Callout>

The code will create a todo and update the todo list each time a todo is created.

Next step is to update and delete the todos. For that, create `updateTodo` and `deleteTodo` functions in the `TodoViewModel.swift` file with the following code:

```swift title="TodoViewModel.swift"
@MainActor
class TodoViewModel: ObservableObject {
    @Published var todos: [Todo] = []

    func createTodo() {
        // ...
    }

    func listTodos() {
        // ...
    }

    func deleteTodos(indexSet: IndexSet) async {
        for index in indexSet {
            do {
                let todo = todos[index]
                let result = try await Amplify.API.mutate(request: .delete(todo))
                switch result {
                case .success(let todo):
                    print("Successfully deleted todo: \(todo)")
                    todos.remove(at: index)
                case .failure(let error):
                    print("Got failed result with \(error.errorDescription)")
                }
            } catch let error as APIError {
                print("Failed to deleted todo: ", error)
            } catch {
                print("Unexpected error: \(error)")
            }
        }
    }

    func updateTodo(todo: Todo) async {
        do {
            let result = try await Amplify.API.mutate(request: .update(todo))
            switch result {
            case .success(let todo):
                print("Successfully updated todo: \(todo)")
            case .failure(let error):
                print("Got failed result with \(error.errorDescription)")
            }
        } catch let error as APIError {
            print("Failed to updated todo: ", error)
        } catch {
            print("Unexpected error: \(error)")
        }
    }
}

```

Update the `List` in the `ContentView.swift` file with code to fetch the todos when the View is displayed and to call `deleteTodos(indexSet:)` when the user left-swipe a todo.

```swift title="ContentView.swift"
struct ContentView: View {
    @StateObject var vm = TodoViewModel()

    var body: some View {
        Authenticator { state in
            VStack {
                // ... Sign out Button
                List {
                    ForEach($vm.todos, id: \.id) { todo in
                        TodoRow(vm: vm, todo: todo)
                    }
                    .onDelete { indexSet in
                        Task { await vm.deleteTodos(indexSet: indexSet) }
                    }
                }
                .task {
                    await vm.listTodos()
                }
                // ... Add new Todo button
            }
        }
    }
}
```

Lastly, create a new file called `TodoRow.swift` with the following code:

```swift title="TodoRow.swift"
import SwiftUI

struct TodoRow: View {
    @ObservedObject var vm: TodoViewModel
    @Binding var todo: Todo

    var body: some View {
        Toggle(isOn: $todo.isDone) {
            Text(todo.content ?? "")
        }
        .toggleStyle(.switch)
        .onChange(of: todo.isDone) { _, newValue in
            var updatedTodo = todo
            updatedTodo.isDone = newValue
            Task { await vm.updateTodo(todo: updatedTodo) }
        }
    }
}

#Preview {
    @State var todo = Todo(content: "Hello Todo World 20240706T15:23:42.256Z", isDone: false)
    return TodoRow(vm: TodoViewModel(), todo: $todo)
}
```

This will update the UI to show a toggle to update the todo `isDone` and a swipe to delete the todo. Now if you run the application you should see the following flow.

<Video width="40%" src="/images/gen2/getting-started/ios/ios-getting-started-3.mp4" description="Video - Delete Demo" />

You can terminate the sandbox environment now to clean up the project.

## Publishing changes to cloud

Publishing changes to the cloud requires a remote git repository. Amplify offers fullstack branch deployments that allow you to automatically deploy infrastructure and application code changes from feature branches. To learn more, visit the [fullstack branch deployments guide](/[platform]/deploy-and-host/fullstack-branching/branch-deployments).

# | "android"

* goal
  1. deploy an Amplify backend database and authentication
  2. connect to the backend from an Android app
  3. make backend updates

## Deploy Amplify backend to AWS

To get started faster, we've created a starter "To-do" Amplify backend. First, create a repository in your GitHub account using our starter template.

### 1. Create repository

Use our starter template to create a repository in your GitHub account. This template scaffolds an Amplify Backend with Auth and Data capabilities.
<ExternalLinkButton
  size="medium"
  href='https://github.com/new?template_name=amplify-backend-template&template_owner=aws-samples&name=amplify-backend-template&description=My%20Amplify%20Gen%202%20starter%20application'
>
<IconGithub />
Create repository from template
</ExternalLinkButton>

### 2. Deploy starter

Now that the repository has been created, deploy this to Amplify's CI/CD pipeline.

<ExternalLinkButton
  size="medium"
  variation="primary"
  href='https://us-east-1.console.aws.amazon.com/amplify/create/repo-branch'
>
<IconAmplify />
Deploy to AWS
</ExternalLinkButton>

Select **GitHub**, pick the starter repository, and hit "Save and Deploy".

<Video src="/images/gen2/getting-started/react/deploy.mp4" description="Video - Deploy the starter app" />

### 3: View deployed backend

<Accordion title='Learn about the project structure' headingLevel='4' eyebrow='While you are waiting for your app to deploy (~5 mins)'>

Let's take a tour of the project structure in this starter repository. The starter application already has pre-written code to give you a real-time database with a feed of all to-do items and the ability to add new items.

```text
├── amplify/ # Folder containing your Amplify backend configuration
│   ├── auth/ # Definition for your auth backend
│   │   └── resource.ts
│   ├── data/ # Definition for your data backend
│   │   └── resource.ts
|   ├── backend.ts
│   └── package.json
```
</Accordion>

 When the build completes, visit the newly deployed branch by selecting the branch name and then looking at the **Deployed backend resources** section under deployments.

<Video src="/images/gen2/getting-started/react/branch-details.mp4" description="Video - Deployments" />

## Make app updates

Let's learn how to enhance the app functionality by creating a delete flow for to-do list items.

### 4. Set up local environment

<Accordion title='Setup Android project' headingLevel='4' eyebrow='If you do not have an existing Android app.'>

**Open Android Studio.** Select **+ Create New Project.**

![Shows the Android studio welcome window](/images/lib/getting-started/android/set-up-android-studio-welcome.png)

In **Select a Project Template**, select **Empty Activity** or **Empty Compose Activity**. Press **Next**.

![Shows Android studio new project window](/images/lib/getting-started/android/set-up-android-studio-select-project-template.png)

- Enter _MyAmplifyApp_ in the **Name** field
- Select either _Java_ or _Kotlin_ from the **Language** dropdown menu
- Select _API 24: Android 7.0 (Nougat)_ from the **Minimum SDK** dropdown menu
- Press **Finish**

![Shows Android studio configure project window](/images/lib/getting-started/android/set-up-android-studio-configure-your-project.png)

</Accordion>

On the **Deployed backend resources**, choose **Download outputs file** to download the `amplify_outputs.json` file that contains identifiers for all the deployed backend resources.

![](/images/gen2/getting-started/react/amplify-outputs-download.png)


Now move the `amplify_outputs.json` file you downloaded above to `app/src/main/res/raw` in your Android project. You will now be able to connect to this backend.

<Accordion title='amplify_outputs.json' headingLevel='4' eyebrow='Learn more'>
The **amplify_outputs.json** file contains backend endpoint information, publicly-viewable API keys, authentication flow information, and more. The Amplify client library uses this outputs file to connect to your Amplify Backend.
</Accordion>

### 5. Install dependencies

Amplify uses some modern Java APIs that require desugaring to be added for earlier versions of Android. In your app/build.gradle.kts add the following lines:
```kotlin title="app/build.gradle.kts"
android {
    compileOptions {
        // Support for Java 8 features
        isCoreLibraryDesugaringEnabled = true
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
}

dependencies {
    coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:2.0.3")
}
```

### 6. Implement login UI

The deployed backend application already has a pre-configured auth backend defined in the `amplify/auth/resource.ts` file.

The fastest way to get your login experience up and running is to use our Authenticator UI component. To use the Authenticator UI component, you need to add the following dependencies to your app/build.gradle.kts file:

<Callout warning>Be sure to have compileSdk version as 34 or higher.</Callout>

```kotlin title="app/build.gradle.kts"
dependencies {
    implementation("com.amplifyframework.ui:authenticator:ANDROID_AUTHENTICATOR_VERSION")
    coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:2.0.3")
}
```

Afterwards create a `MyAmplifyApp` class that extends `Application` and add the following code:


```kotlin title="MyAmplifyApp.kt"
import android.app.Application
import android.util.Log
import com.amplifyframework.AmplifyException
import com.amplifyframework.auth.cognito.AWSCognitoAuthPlugin
import com.amplifyframework.core.Amplify
import com.amplifyframework.core.configuration.AmplifyOutputs

class MyAmplifyApp: Application() {
    override fun onCreate() {
        super.onCreate()

        try {
            Amplify.addPlugin(AWSCognitoAuthPlugin())
            Amplify.configure(AmplifyOutputs(R.raw.amplify_outputs), applicationContext)
            Log.i("MyAmplifyApp", "Initialized Amplify")
        } catch (error: AmplifyException) {
            Log.e("MyAmplifyApp", "Could not initialize Amplify", error)
        }
    }
}
```

Next call this class in your `AndroidManifest.xml` file:

```xml title="AndroidManifest.xml"
<application
    android:name=".MyAmplifyApp"
    ...
</application>
```

Update `MainActivity.kt` to use the Android Authenticator component.

```kotlin title="MainActivity.kt"
import android.os.Bundle
..
//highlight-start
import com.amplifyframework.ui.authenticator.ui.Authenticator
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.Button
import com.amplifyframework.core.Amplify
//highlight-end

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyAmplifyAppTheme {
                // A surface container using the 'background' color from the theme
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                )
                //highlight-start
                {
                    Authenticator { state ->
                        Column {
                            Text(
                                text = "Hello ${state.user.username}!",
                            )
                            Button(onClick = {
                                Amplify.Auth.signOut {  }
                            }) {
                                Text(text = "Sign Out")
                            }
                        }
                    }
                }
                //highlight-end
            }
        }
    }
}
```

Now if you run the application on the Android emulator, you should see the authentication flow working.

<Video width="40%" src="/images/gen2/getting-started/android/android-getting-started-1.mp4" description="Video - Authentication Demo" />

### 7. Read and write data

The initial scaffolding already has a pre-configured data backend defined in the `amplify/data/resource.ts` file. The default example will create a Todo model with `content` field.

Amplify can automatically generate code for interacting with the backend API. The command below generates model classes from the Data schema:


<Accordion title='Looking for your App ID?' headingLevel='4' eyebrow='Find your App ID in the Amplify Console'>
In the command below, replace the APP-ID with your Amplify app ID. Find this in the Amplify Console.
![image](images/gen2/getting-started/appid.png)
</Accordion>


```bash title="Terminal" showLineNumbers={false}
cd my-android-app
npx @aws-amplify/backend-cli generate graphql-client-code --format modelgen --model-target java --out app/src/main/java --app-id <your-amplify-app-id> --branch main
```

Once you are done, add the following dependencies to your project:

```kotlin title="build.gradle.kts"
dependencies {
    // Amplify API dependencies
    // highlight-start
    implementation("com.amplifyframework:aws-api:ANDROID_VERSION")
    // highlight-end
    // ... other dependencies
}
```

After adding the dependencies, open the `MyAmplifyApp` class and add the following line before the `configure` call:

```kotlin title="MyAmplifyApp.kt"

// highlight-next-line
import com.amplifyframework.api.aws.AWSApiPlugin

..
// highlight-next-line
Amplify.addPlugin(AWSApiPlugin())
```

Update the `MainActivity` class with the following code to create new to-do items. The `onClick` function will create a new Todo item.

```kt title="MainActivity"

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyAmplifyAppTheme {
                // A surface container using the 'background' color from the theme
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    Authenticator { state ->
                        Column {
                            Text(
                                text = "Hello ${state.user.username}!",
                            )
                            // highlight-start
                            Button(onClick = {
                                val todo = Todo.builder()
                                    .content("My first todo")
                                    .build()

                                Amplify.API.mutate(
                                    ModelMutation.create(todo),
                                    { Log.i("MyAmplifyApp", "Added Todo with id: ${it.data.id}")},
                                    { Log.e("MyAmplifyApp", "Create failed", it)},
                                )
                            }) {
                                Text(text = "Create Todo")
                            }
                            // highlight-end
                            Button(onClick = {
                                Amplify.Auth.signOut {  }
                            }) {
                                Text(text = "Sign Out")
                            }
                        }
                    }
                }
            }
        }
    }
}
```

Now it is time to add a logic to view the added items.

```kt title="MainActivity.kt"
@Composable
fun TodoList() {
    var todoList by remember { mutableStateOf(emptyList<Todo>()) }

    LaunchedEffect(Unit) {
        // API request to list all Todos
        Amplify.API.query(
            ModelQuery.list(Todo::class.java),
            { todoList = it.data.items.toList() },
            { Log.e("MyAmplifyApp", "Failed to query.", it) }
        )
    }

    LazyColumn {
        items(todoList) { todo ->
            Row {
                // Render your activity item here
                Text(text = todo.content)
            }
        }
    }
}
```

Now call `TodoList()` from the `onCreate()` function:

```kt title="MainActivity.kt"
setContent {
    MyAmplifyAppTheme {
        // A surface container using the 'background' color from the theme
        Surface(
            modifier = Modifier.fillMaxSize(),
            color = MaterialTheme.colorScheme.background
        ) {
            Authenticator { state ->
                Column {
                    Text(
                        text = "Hello ${state.user.username}!",
                    )
                    ....
                    //highlight-next-line
                    TodoList()
```

### 8. Enable real-time data

If you build and rerun the application, you should see the todo that was created in the previous build. But notice how when you click on the "create Todo" button, it doesn't add any new todos to the list below until the next time your app relaunches. To solve this, let's add real-time updates to the todo list.

To add real-time updates, you can use the subscription feature of Amplify Data. It allows to subscribe to `onCreate`, `onUpdate`, and `onDelete` events of the application. In our example, let's append the list every time a new todo is added.

```kt title="MainActivity.kt"
@Composable
fun TodoList() {
    var todoList by remember { mutableStateOf(emptyList<Todo>()) }

    LaunchedEffect(Unit) {
        Amplify.API.query(
            ModelQuery.list(Todo::class.java),
            { todoList = it.data.items.toList() },
            { Log.e("MyAmplifyApp", "Failed to query.", it) }
        )
        // highlight-start
        Amplify.API.subscribe(
            ModelSubscription.onCreate(Todo::class.java),
            { Log.i("ApiQuickStart", "Subscription established") },
            {
                Log.i("ApiQuickStart", "Todo create subscription received: ${it.data}")
                todoList = todoList + it.data
            },
            { Log.e("ApiQuickStart", "Subscription failed", it) },
            { Log.i("ApiQuickStart", "Subscription completed") }
        )
        // highlight-end
    }

    LazyColumn {
        items(todoList) { todo ->
            Row {
                // Render your activity item here
                Text(text = todo.content)
            }
        }
    }
}
```


## Make backend updates

Let's update our backend to implement per-user authorization rules, allowing each user to only access their own to-dos.

### 9. Implement per-user authorization

First, clone the deployed repository.

```bash title="Terminal" showLineNumbers={false}
git clone https://github.com/<github-user>/amplify-backend-template.git
cd amplify-backend-template
npm install
```
The backend to-do model is configured to share data across all users, but, in most cases, you want data to be isolated on a per-user basis.

To isolate the data on a per-user basis, you can use an "owner-based authorization rule". Let's apply the owner-based authorization rule to your to-do items:

```ts title="amplify/data/resource.ts"
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a.model({
    content: a.string(),
    // highlight-next-line
  }).authorization(allow => [allow.owner()]),
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    // This tells the data client in your app (generateClient())
    // to sign API requests with the user authentication token.
    // highlight-next-line
    defaultAuthorizationMode: 'userPool',
  },
});
```
Commit this change to your git repository. Amplify's CI/CD system will automatically pick up the changes and build and deploy the updates.


```bash title="Terminal" showLineNumbers={false}
git commit -am "added per-user data isolation"
git push
```
### 10. Test in app

Now, let's go back to your Android application and test out the user isolation of the to-do items. Fetch the latest `amplify_outputs.json` and model files by re-running the following command in your Android Studio terminal.

<Accordion title='Looking for your App ID?' headingLevel='4' eyebrow='Find your App ID in the Amplify Console'>
In the command below, replace the APP-ID with your Amplify app ID. Find this in the Amplify Console.

![image](images/gen2/getting-started/appid.png)
</Accordion>

```bash showLineNumbers={false}
npx @aws-amplify/backend-cli generate graphql-client-code --format modelgen --model-target java --out app/src/main/java --app-id <your-amplify-app-id> --branch main
```

Also update your `amplify_outputs` file with the latest outputs information.

```bash showLineNumbers={false}
npx @aws-amplify/backend-cli generate outputs --out-dir app/src/main/res/raw --app-id <your-amplify-app-id> --branch main
```

# | "react-native"

## Prerequisites

Before you get started, make sure you have the following installed:

- [Node.js](https://nodejs.org/) v18.17 or later
- [npm](https://www.npmjs.com/) v9 or later
- [git](https://git-scm.com/) v2.14.1 or later
- You will also need to [create an AWS Account](https://portal.aws.amazon.com/billing/signup). Note that AWS Amplify is part of the [AWS Free Tier](https://aws.amazon.com/amplify/pricing/).
- Configure your AWS account to use with Amplify [instructions](/[platform]/start/account-setup/).


This Quickstart guide will walk you through how to build a Todo application for Android or iOS using [Expo](https://expo.dev/)'s TypeScript template.

<Callout warning>

**Warning:** React Native for Web is not officially supported yet, but we are working towards official support. We are tracking the progress in [issue #13918 on GitHub](https://github.com/aws-amplify/amplify-js/issues/13918)

</Callout>

<Callout>

Amplify now requires native modules not available through the Expo SDK. As a result, Expo Go is no longer supported but you should still be able to use Expo. [Learn more about dropping support for Expo Go in Amplify v6](/gen1/react-native/build-a-backend/troubleshooting/migrate-from-javascript-v5-to-v6/).

</Callout>

```bash title="Terminal" showLineNumbers={false}
npx create-expo-app my_amplify_app -t expo-template-blank-typescript
cd my_amplify_app
```

<Callout warning>
For calling native libraries and platform dependencies, you need to have run the prebuild command for generating the folders for depending platforms.

```bash title="Terminal" showLineNumbers={false}
npx expo prebuild
```
</Callout>

## Create Backend

The easiest way to get started with AWS Amplify is through npm with `create-amplify` command. You can run it from your base project directory.

```bash title="Terminal" showLineNumbers={false}
cd my_amplify_app
npm create amplify@latest
? Where should we create your project? (.) # press enter
```

Running this command will scaffold Amplify backend files in your current project with the following files added:

```text
├── amplify/
│   ├── auth/
│   │   └── resource.ts
│   ├── data/
│   │   └── resource.ts
│   ├── backend.ts
│   └── package.json
├── node_modules/
├── .gitignore
├── package-lock.json
├── package.json
└── tsconfig.json
```

To deploy your backend use Amplify's per-developer cloud sandbox. This feature provides a separate backend environment for every developer on a team, ideal for local development and testing. To run your application with a sandbox environment, you can run the following command:

```bash title="Terminal" showLineNumbers={false}
npx ampx sandbox
```

### Adding Authentication

The initial scaffolding already has a pre-configured auth backend defined in the `amplify/auth/resource`.ts file. We've configured it to support email and password login but you can extend it to support a variety of login mechanisms, including Google, Amazon, Sign In With Apple, and Facebook.

The fastest way to get your login experience up and running is to use our Authenticator UI component available in the Amplify UI library.

To use the Authenticator, you need to add the following dependencies to your project:

```bash title="Terminal" showLineNumbers={false}
npm add \
  @aws-amplify/ui-react-native \
  @aws-amplify/react-native \
  aws-amplify \
  @react-native-community/netinfo \
  @react-native-async-storage/async-storage \
  react-native-safe-area-context \
  react-native-get-random-values \
  react-native-url-polyfill
```

Then install the iOS cocoapods for targeting iOS by running:

```bash title="Terminal" showLineNumbers={false}
npx pod-install
```

Next, update the `App.tsx` file with the following:

```typescript
import React from "react";
import { Button, View, StyleSheet, SafeAreaView } from "react-native";

import { Amplify } from "aws-amplify";
import { Authenticator, useAuthenticator } from "@aws-amplify/ui-react-native";

import outputs from "./amplify_outputs.json";

Amplify.configure(outputs);

const SignOutButton = () => {
  const { signOut } = useAuthenticator();

  return (
    <View style={styles.signOutButton}>
      <Button title="Sign Out" onPress={signOut} />
    </View>
  );
};

const App = () => {
  return (
    <Authenticator.Provider>
      <Authenticator>
        <SafeAreaView>
          <SignOutButton />
        </SafeAreaView>
      </Authenticator>
    </Authenticator.Provider>
  );
};

const styles = StyleSheet.create({
  signOutButton: {
    alignSelf: "flex-end",
  },
});

export default App;
```

The Authenticator component auto-detects your auth backend settings and renders the correct UI state based on the auth backend's authentication flow.


Run your application in your local environment again. You should be presented with a login experience now.

<Video src="/images/gen2/getting-started/reactnative/reactnative-getting-started-1.mp4" description="Video - Authentication Demo" />

## Adding Data

The initial scaffolding already has a pre-configured data backend defined in the `amplify/data/resource.ts` file. The default example will create a Todo model with `content` field.

Let's modify this to add the following:
- A boolean `isDone` field.
- An authorization rules specifying owners, authenticated via your Auth resource can "create", "read", "update", and "delete" their own records.
- Update the `defaultAuthorizationMode` to sign API requests with the user authentication token.

```typescript
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';

const schema = a.schema({
  Todo: a
    .model({
      content: a.string(),
      isDone: a.boolean()
    })
    .authorization(allow => [allow.owner()])
});

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: 'userPool'
  }
});
```

Next, let's implement UI to create, list, and delete the to-do items. Create a `src` folder, and within the folder, create a new file called `TodoList.tsx`. This page will contain information about creating, reading, updating, and deleting Todo items.

Copy and paste the following code into the file:

```typescript
import { useState, useEffect } from "react";
import { View, Button, Text, StyleSheet, FlatList } from "react-native";

import { generateClient } from "aws-amplify/data";
import type { Schema } from "../amplify/data/resource";
import { GraphQLError } from "graphql";
const client = generateClient<Schema>();

const TodoList = () => {
  const dateTimeNow = new Date();
  const [todos, setTodos] = useState<Schema["Todo"]["type"][]>([]);
  const [errors, setErrors] = useState<GraphQLError>();

  useEffect(() => {
    const sub = client.models.Todo.observeQuery().subscribe({
      next: ({ items }) => {
        setTodos([...items]);
      },
    });

    return () => sub.unsubscribe();
  }, []);

  const createTodo = async () => {
    try {
      await client.models.Todo.create({
        content: `${dateTimeNow.getUTCMilliseconds()}`,
      });
    } catch (error: unknown) {
      if (error instanceof GraphQLError) {
        setErrors(error);
      } else {
        throw error;
      }
    }
  };

  if (errors) {
    return <Text>{errors.message}</Text>;
  }

  const renderItem = ({ item }: { item: Schema["Todo"]["type"] }) => (
    <TodoItem {...item} />
  );
  return (
    <View style={{ flex: 1 }}>
      <FlatList
        data={todos}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        ItemSeparatorComponent={() => (
          <View style={styles.listItemSeparator} />
        )}
        ListEmptyComponent={() => <Text>The todo list is empty.</Text>}
        style={styles.listContainer}
      ></FlatList>
      <Button onPress={createTodo} title="Create Todo" />
    </View>
  );
};

const TodoItem = (todo: Schema["Todo"]["type"]) => (
  <View style={styles.todoItemContainer} key={todo.id}>
    <Text
      style={{
        ...styles.todoItemText,
        textDecorationLine: todo.isDone ? "line-through" : "none",
        textDecorationColor: todo.isDone ? "red" : "black",
      }}
    >
      {todo.content}
    </Text>
    <Button
      onPress={async () => {
        await client.models.Todo.delete(todo);
      }}
      title="Delete"
    />
    <Button
      onPress={() => {
        client.models.Todo.update({
          id: todo.id,
          isDone: !todo.isDone,
        });
      }}
      title={todo.isDone ? "Undo" : "Done"}
    />
  </View>
);

const styles = StyleSheet.create({
  todoItemContainer: { flexDirection: "row", alignItems: "center", padding: 8 },
  todoItemText: { flex: 1, textAlign: "center" },
  listContainer: { flex: 1, alignSelf: "stretch", padding:8 },
  listItemSeparator: { backgroundColor: "lightgrey", height: 2 },
});

export default TodoList;
```

With the code above, you can create a random todo item and display todo items in a list. You can mark them as done, update the list, or revert that operation. You can also delete the items. Each change in the list is listened to with the subscription and immediately shown on the screen.

If we take a closer look at the code:
- `generateClient` generates the necessary files and folders for models.
- `TodoList` component includes the subscription, creation operations, and a list to hold created items.
- `TodoItem` holds the information about each todo item.

Lastly, update the `App` component in `App.tsx` as follows:

```typescript
const App = () => {
  return (
    <Authenticator.Provider>
      <Authenticator>
        <SafeAreaView style={styles.container}>
          <SignOutButton />
          <TodoList />
        </SafeAreaView>
      </Authenticator>
    </Authenticator.Provider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 8,
  },
  signOutButton: {
    alignSelf: "flex-end",
  },
});
```

If you run the application now, you should see the following behavior:

<Video src="/images/gen2/getting-started/reactnative/reactnative-getting-started-2.mp4" description="Video - Delete Demo" />

You can terminate the sandbox environment now to clean up the project.

### Publishing changes to cloud

Publishing changes to the cloud requires a remote git repository.
Amplify offers fullstack branch deployments that allow you to automatically deploy infrastructure and application code changes from feature branches.
To learn more, visit the [fullstack branch deployments guide](/[platform]/deploy-and-host/fullstack-branching/branch-deployments).