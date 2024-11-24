* AWS Amplify
  * allows, about cloud-powered web and mobile apps,
    * develop
      * TypeScript experience
        * | `webPlatform`
          * uses
            * develop your frontend & backend / 
              * schema validation,
              * dot completion,
              * E2E types
          * see [here](how-amplify-works/concepts/index.md/#build-fullstack-apps-with-typescript)
        * | `mobilePlatform`
          * uses
            * define your data -> handle the
              * model
              * configuration file generations
          * see [here](build-a-backend/data/set-up-data/)
      * | `webPlatform`
        * real-time data
          * frontend state -- is sync to -- backend updates
            * == write TypeScript / NO think about WebSockets
          * see [here](build-a-backend/data/set-up-data)
        * auto-generate CRUD forms / -- wired to -- data
          * CRUD forms -- are mapped to -- your data model /
            * form-level validations
            * error states built in
          * see [here](build-ui)
      * Authentication and authorization -- for -- secure apps
        * authentication strategies
          * _Example:_ passwords, social, email links
        * authorizations -- based on -- users and groups
        * see [here](build-a-backend/auth/set-up-auth/)
    * deploy
      * | `webPlatform`
        * SSR/SSG/ISR hosting support
          * if you connect your Git repository -> you can deploy web apps
          * see [here](deploy-and-host/hosting)
      * faster iterations -- via -- sandboxes / per-developer
        * see [here](deploy-and-host/sandbox-environments/setup)
      * Zero-config fullstack branches
        * from Git branches -- fullstack deployments / set up -- staging, development, and production environments
        * see [here](deploy-and-host/fullstack-branching/branch-deployments)
      * GUI to manage your data
        * manage | 1! console, your app
          * data,
          * users and groups
          * files 
        * see [here](how-amplify-works/concepts)
    * connecting easily your applications -- to -- AWS
      * uses
        * data modeling,
        * authentication,
        * storage,
        * serverless functions
    * customizations
      * Add any AWS service -- via -- CDK
        * -- can access to -- 200+ AWS services
        * see [here](build-a-backend/add-aws-services)
      * Bring your own pipelines
        * uses
          * | deployments
            * cross-account or multi-region,
            * stage-based
        * see [here](deploy-and-host/fullstack-branching/custom-pipelines)
      * Monorepo and multi-repo support
        * allowed fullstack team workflows
          * monorepos,
          * micro frontends,
          * multi-repos
          * ...
        * see [here](deploy-and-host/fullstack-branching/mono-and-multi-repos)
  * available | `platforms`
  * features
    * Code-first DX
      * | `mobilePlatform`
        * == define your -- via -- TypeScript
      * | rest of `platforms`
        * == focus | your app code / NOT | infrastructure
    * Fullstack Git deployments / Git Ops
      * deploy your frontend and backend together / EVERY code commit
      * == Git branch == source of truth
    * Faster local development 
      * -- via -- cloud sandbox environments / developer
      * allows
        * quickly iterate during development

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
const mobilePlatform = ['swift', 'android', 'flutter']
const webPlatform = ['android', 'angular', 'javascript', 'nextjs', 'react', 'react-native', 'vue']
