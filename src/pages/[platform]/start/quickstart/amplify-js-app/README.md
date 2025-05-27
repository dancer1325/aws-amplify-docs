## How has it been created?
* create a new Vanilla JavaScript app -- via -- vite 
  ```bash
  npm create vite@latest
  ✔ Project name: amplify-js-app
  ✔ Select a framework: › Vanilla
  ✔ Select a variant: › TypeScript
  ```
*
  ```bash
  cd amplify-js-app
  npm install
  npm run dev
  ```
* open [http://localhost:5173](http://localhost:5173) | browser
* modify 
  * `index.html`
  * `style.css`
  * `main.js` / leave it empty

## How to create the backend?
```bash title="Terminal" showLineNumbers={false}
npm create amplify@latest
```
* scaffold Amplify backend files | your current project ("amplify/")
  
  ```text
  ├── amplify/
  │   ├── auth/
  │   │   └── resource.ts
  │   ├── data/
  │   │   └── resource.ts
  │   ├── backend.ts
  │   └── package.json
  ├── node_modules/
  ├── index.html
  ├── style.css
  ├── .gitignore
  ├── package-lock.json
  ├── package.json
  └── tsconfig.json
  ```

## How to set up local AWS credentials
* uses
  * deploy backend updates -- from -- our local machine
* see [set up local AWS credentials](/[platform]/start/account-setup/)    

## How to deploy cloud sandbox?
* Amplify's per-developer cloud sandbox
  * allows
    * deploy your backend
  * separate backend environment / EVERY developer on a team
  * uses
    * local development
    * testing
* 
    ```bash title="Terminal" showLineNumbers={false}
    npx ampx sandbox
    ```
  * 👀if you have NOT yet bootstrapped your account -> you need to run 2 times 👀
    * Sandbox commands do NOT deploy / provision resources | AWS
  * it will create
    * GraphQL API,
      * TODO: Why? & Where?
    * database,
      * see `amplify/data/resource.ts`
    * auth service
      * TODO: Why? & Where?
  * ALL the deployed resources are available | `amplify_outputs.json`
    * "us-west-2"
      * Reason: I have got `AWS_DEFAULT_REGION=us-west-2` environment variable
    * NO track in git

## Connect FE -- to -- BE

* TODO:
The initial scaffolding already has a pre-configured data backend defined in the `amplify/data/resource.ts` file. 
The default example will create a Todo model with `content` field. 
* Update your main.js file to create new to-do items.

```typescript title="src/main.ts"
import { generateClient } from "aws-amplify/data";
import type { Schema } from "../amplify/data/resource";
import './style.css';
import { Amplify } from 'aws-amplify';
import outputs from '../amplify_outputs.json';

Amplify.configure(outputs);


const client = generateClient<Schema>();

document.addEventListener("DOMContentLoaded", function () {
    const todos: Array<Schema["Todo"]["type"]> = [];
    const todoList = document.getElementById("todoList") as HTMLUListElement;
    const addTodoButton = document.getElementById("addTodo") as HTMLButtonElement;

    addTodoButton.addEventListener("click", createTodo);

    function updateUI() {
        todoList.innerHTML = '';
        todos.forEach(todo => {
            const li = document.createElement('li');
            li.textContent = todo.content ?? '';
            todoList.appendChild(li);
        });
    }

    function createTodo() {
      console.log('createTodo');
      const content = window.prompt("Todo content");
      if (content) {
          client.models.Todo.create({ content }).then(response => {
              if (response.data && !response.errors) {
                  todos.push(response.data);
                  updateUI();
              } else {
                  console.error('Error creating todo:', response.errors);
                  alert('Failed to create todo.');
              }
          }).catch(error => {
              console.error('Network or other error:', error);
              alert('Failed to create todo due to a network or other error.');
          });
      }
  }


    client.models.Todo.observeQuery().subscribe({
        next: (data) => {
            todos.splice(0, todos.length, ...data.items);
            updateUI();
        }
    });
});
```


