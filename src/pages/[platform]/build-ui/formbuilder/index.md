* Connected Forms
  * 👀-- are bound to a -- model | your app's data schema 👀
  * once it's submitted -> record is automatically created or updated | bound data model
    * / SOME or ALL of the form's input fields -- are mapped to -- fields | data model
  * 👀automatically work with any Amplify GraphQL API /👀
    * 👀NO `onSubmit` handling is required 👀

## Generate forms

* steps
  * install the Amplify UI library

    ```bash title="Terminal" showLineNumbers={false}
    npm add @aws-amplify/ui-react
    ```

* TODO:
To use connected forms, you first need to deploy a data model from your sandbox environment. We will use the same example as in the getting started [tutorial](/[platform]/start/quickstart).
To get started run the following command from your project root:

```bash title="Terminal" showLineNumbers={false}
npx ampx generate forms
```

This will generate create and update forms for each model defined in your schema in a folder called `ui-components`.

```bash title="Terminal" showLineNumbers={false}
File written: ui-components/graphql/subscriptions.ts
File written: ui-components/graphql/mutations.ts
File written: ui-components/graphql/queries.ts
File written: ui-components/TodoCreateForm.jsx
File written: ui-components/TodoCreateForm.d.ts
File written: ui-components/TodoUpdateForm.jsx
File written: ui-components/TodoUpdateForm.d.ts
File written: ui-components/utils.js
File written: ui-components/index.js
```


### Re-generating forms

In Gen 2, we automatically generate the form UI for you, which you can then customize and manage. If you decide to update your data model and need to regenerate the forms, please ensure you back up the original `ui-components` folder before executing the `npx ampx generate forms` command again.

## Render React form in your app

1. In your application's entrypoint file (e.g. `src/index.js` for create-react-app or `src/main.jsx` for Vite), add the following imports and configuration

```jsx
// highlight-start
import '@aws-amplify/ui-react/styles.css';
import { ThemeProvider } from '@aws-amplify/ui-react';
// highlight-end
import { Amplify } from 'aws-amplify';

import outputs from './amplify_outputs.json';

Amplify.configure(outputs);
```

2. In your application's entrypoint file (e.g. `src/main.jsx` for Vite), wrap the `<App />` component with the following:

```jsx
<ThemeProvider>
  <App />
</ThemeProvider>
```

3. Import your form by name. For a form named `TodoCreateForm`, you would use the following code:

```jsx
import { TodoCreateForm } from './ui-components';
```

3. Place your form in code. For a form named `ProductCreateForm` in a React project, you could use the following App code:

```jsx
function App() {
  return <TodoCreateForm />;
}

export default App;
```

## Types of forms

All connected and unconnected forms are either a **Create** form or an **Update** form.

### Create forms

Create forms render a form with empty inputs. If a create form is connected to a data model, will always generate a new record upon submission.

### Update forms

Update forms expect an input value in order to pre-populate the form.

For update forms that are connected to a data model, you can use the `id` prop, or the model prop:

- `id` prop: id string of the record you want to update. For example:

```jsx
<AuthorUpdateForm id="ac74af5c-3aab-4274-8f41-23e1e6576af5" />
```

- Model prop: if your form is bound to a data model named `Author`, your form will have a prop named `author` as well, which can receive a record. For example:

```jsx

<AuthorUpdateForm author={authorRecord}>

```
