* goal
  * what Amplify Auth
    * provisions
    * supports
    
* security strategy
  * == authentication method + security credentials + additional verification

- _Authentication_ 
  - == process / validate **who you are**
  - _AuthN_
    - abbreviation
  - Identity Provider or IdP
    - == system / does this validation
    - types
      - own self-hosted IdP
      - cloud service
- _Authorization_
  - == process / validate **what you can access**
  - _AuthZ_
    - abbreviation
  - ways to do it
    - tokens / custom logic,
    - predefined rules,
    - signed requests with policies

* security credentials
  * recommendations
    * modify the default password policy / ensure your users -- create -- stronger passwords 
* additional verification
  * recommendations
    * require additional contact information | BEFORE reset passwords
    * enable MFA

## What is Amazon Cognito?

* TODO:
Amplify Auth is powered by [Amazon Cognito](https://aws.amazon.com/cognito/). Amazon Cognito is an identity and access management service, enabling you to secure your web or mobile applications, and is comprised of two services:

1. [Amazon Cognito User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) is a full-featured user directory service to handle user registration, authentication, and account recovery
2. [Amazon Cognito Federated Identities or Identity Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) is a service used to authorize your users to interact with other AWS services

Amplify interfaces with User Pools to store your user information, including federation with other OpenID providers like Apple, Facebook, Google, or Amazon, and leverages federated identities to manage user access to AWS resources.

Authorization is often done in one of two ways:

1. Clients pass the tokens to the backend that perform custom logic to allow or deny actions
2. Clients sign the requests and the backend validates the signature, allowing or denying actions depending on predefined policy. The predefined rules, known as [IAM access policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html), are automatically configured by Amplify.

The first is a common authorization method for HTTP or GraphQL APIs, while the second is necessary for interfacing with AWS services such as Amazon S3, Amazon Pinpoint, and others.

### Before you build

Amazon Cognito can be customized based on your security strategy for authentication. However, some initial configuration options cannot be changed after the backend resources are configured:

- User attributes that are used to identify your individual users (such as email and phone) cannot be renamed or deleted.
- Sign-in methods (including username, email, and phone) cannot be added or changed after the initial configuration. This includes both defining which attributes are used to sign in and which attributes are required. Required attributes must have a value for all users once set.
- Verification methods (including username and email) are the same as required attributes and cannot be removed once configured.
- The `sub` attribute is a unique identifier within each user pool that cannot be modified and can be used to index and search users.
- If MFA is set to **required** with phone number for all users, you will need to include MFA setup (i.e. mandating phone number) when users sign up.

Visit the [Amazon Cognito documentation](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) for more details on these settings, including [User pool attributes](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-attributes.html) and [Adding MFA to a user pool](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-mfa.html).
