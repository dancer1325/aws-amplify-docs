* **Ways of working:**
  * `next-release/main`
    * commit changes / larger site update
  * 1 branch / page
    * branch from `next-release/main`
    * Reason: 🧠reduce the size of PRs🧠
  * PR Reviews
    * submit PRs to `next-release/main`

* **New IA:**
  * this folder's menu == new docs IA organization 
  * do NOT change `gen2`'s first-level folders ("build-a-backend", "build-ui", etc.) 
  * EVERY new menu's entry can be selected -> every folder requires an "index.mdx"

* **Folder structure:**
  * TODO: Pages folders that host the root file (index.mdx) continue to drive the final URL. 
  * Use descriptive naming conventions for these folders to produce descriptive and SEO-friendly URLs.
  * For example, src/pages/gen2/build-a-backend/auth/set-up-auth/index.mdx for https://docs.amplify.aws/gen2/build-a-backend/auth/set-up-auth/.
  * Please use the index.mdx naming convention for your root folder files.

**Platform switcher:** There is not a platform switcher for this folder, but we are organizing the folder and file structure to work with a switcher in the future. The new platform switcher will sit at the top of the pages folder and at the start of the URL, for example, src/pages/[platform]/start/getting-started/setup/index.mdx. Please make sure your root folder files use the index.mdx naming convention.

**InlineFilter:** No InlineFilter needed on these pages atm because there is not a switcher. This folder will only be visible to JS-related platforms.

**Fragments:** Do not use any single-use fragments.

**Initial setup:** An initial IA was set up to provide a visual structure for migrating pages. Not all entries are on this page and it is expected that some of the root folder names may change. Please remember to check the notes above when making changes or adding new folders and files.

Updated 10/19
gen2
├── build-a-backend
│   ├── add-aws-services
│   │   ├── custom-resources
│   │   │   └── index.mdx
│   │   └── overriding-resources
│   │       └── index.mdx
│   ├── auth
│   │   ├── add-social-provider
│   │   │   └── index.mdx
│   │   └── set-up-auth
│   │       └── index.mdx
│   ├── data
│   │   ├── connect-existing-data
│   │   │   └── index.mdx
│   │   └── set-up-data
│   │       └── index.mdx
│   ├── functions
│   │   └── index.mdx
│   ├── index.mdx
│   └── storage
│       └── index.mdx
├── build-ui
│   └── index.mdx
├── deploy-and-host
│   ├── fullstack-branching
│   │   ├── branch-deployments
│   │   │   └── index.mdx
│   │   └── sandbox-deployments
│   │       └── index.mdx
│   ├── hosting-features
│   │   └── index.mdx
│   ├── index.mdx
│   └── local-development
│       └── index.mdx
├── how-amplify-works
│   ├── concepts
│   │   └── index.mdx
│   ├── existing-projects
│   │   └── index.mdx
│   ├── index.mdx
│   └── project-structure
│       └── index.mdx
├── README.mdx
├── reference
│   ├── amplifyconfiguration
│   │   └── index.mdx
│   ├── CLI-commands
│   │   └── index.mdx
│   └── index.mdx
└── start
    └── index.mdx
