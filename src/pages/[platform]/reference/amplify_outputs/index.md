* `amplify_outputs.json`
  * == backend's outputs
    * _Example:_ Data endpoint, Auth metadata
  * == "client configuration file" 
  * uses
    * configure the client libraries -- to interact with -- your backend resources 
  * ways to generate it 
    * | Amplify Gen 2
      * -- via -- `ampx sandbox`
    * | Amplify's CI/CD
      * created automatically / -- based on the --
        * current Amplify app ID + git branch
    * [`ampx generate outputs`](/[platform]/reference/cli-commands#npx-ampx-generate-outputs)
      * manually
      * -- based on --
        * Amplify app ID + branch, or
        * AWS CloudFormation stack name 

## Extending Amplify outputs file

* != static artifact
  * Reason: 🧠designed to be extendable / suit the evolving needs of your application 🧠
  * `backend.addOutput`
    * programmatically add configurations 
    * uses
      * customizing outputs / NOT directly exposed -- through the -- Amplify constructs
      * dynamically (== NO manual file) adjust your app's configuration -- in response to -- changes | your backend strategy
* ⚠️NOT supported override Amplify-managed configurations | `amplify_outputs.json` ⚠️

* _Example:_ add output parameters | your `amplify_outputs.json` / specify an S3 bucket and AWS region

    ```ts title="amplify/backend.ts"
    import { defineBackend } from "@aws-amplify/backend";
    import { auth } from "./auth/resource";
    import { data } from "./data/resource";
    
    const backend = defineBackend({
      auth, 
      data, 
    });
    
    backend.addOutput({
      storage: {
        aws_region: "us-east-1",
        bucket_name: "my-externally-managed-bucket",
      },
    });
    ```

    ```ts title="src/index.ts"
    import { Amplify } from "aws-amplify";
    import outputs from "@/amplify_outputs.json";
    
    Amplify.configure(outputs);
    ```

### Custom configuration

* == add custom output parameters | your `amplify_outputs.json` 
* uses
  * surface arbitrary outputs / -- might be necessary for -- your application's logic or configuration
    * _Example:_ values -- from -- custom CDK resources 

* _Example:_ 

    ```ts title="amplify/backend.ts"
    import { defineBackend } from "@aws-amplify/backend";
    import { auth } from "./auth/resource";
    import { data } from "./data/resource";
    
    const backend = defineBackend({
      auth, 
      data, 
    });
    
    backend.addOutput({
      custom: {
        api_id: "restAPIId",
        api_endpoint: "https://api.example.com",
        api_name: "restApiName",
      },
    });
    ```

    ```ts title="src/index.ts"
    import { Amplify } from "aws-amplify";
    import outputs from "@/amplify_outputs.json";
    
    Amplify.configure(outputs);
    const currentConfig = Amplify.getConfig(); 
    Amplify.configure({
      ...currentConfig,
      API: {
        REST: {
          [outputs.custom.api_name]: {
            endpoint: outputs.custom.api_endpoint,
            region: "us-east-1",
          },
        },
      },
    });
    ```

## Schema reference

* [here](https://github.com/aws-amplify/amplify-backend/blob/main/packages/client-config/src/client-config-schema/schema_v1.json).
