* goal
  * how to use the AWS SDK with Amplify v2. | Android

* ⚠️AWS Android Mobile Client (`com.amazonaws:aws-android-sdk-mobile-client`) -- NOT compatible each other -- Amplify Android v2 ⚠️
  * Reason: 🧠Amplify v2 migrates the credentials | AWS Mobile Client -- into a -- different format -> AWS Mobile Client unable to read the credentials 🧠

## Amplify V2 Auth + AWS Android SDK Plugin

* Amplify v2 + [AWS Kotlin SDK](https://aws.amazon.com/sdk-for-kotlin/)
  * 👀rather than AWS Android SDK 👀

### Creating an AmplifyCredentialsProvider

* TODO:
Many of the AWS Android SDK plugins accept a custom `AWSCredentialsProvider` implementation. You can implement your own `AWSCredentialsProvider` that uses Amplify Android v2 to provide credentials. 

<BlockSwitcher>
<Block name="Java">

```java
import com.amazonaws.auth.AWSCredentials;
import com.amazonaws.auth.AWSCredentialsProvider;
import com.amazonaws.auth.BasicSessionCredentials;
import com.amplifyframework.auth.AWSTemporaryCredentials;
import com.amplifyframework.auth.cognito.AWSCognitoAuthSession;
import com.amplifyframework.auth.options.AuthFetchSessionOptions;
import com.amplifyframework.core.Amplify;

import java.util.concurrent.CompletableFuture;

class AmplifyCredentialsProvider implements AWSCredentialsProvider {

    @Override
    public AWSCredentials getCredentials() {
        CompletableFuture<AWSCredentials> sdkCredentials = new CompletableFuture<>();

        Amplify.Auth.fetchAuthSession((authSession) -> {
            BasicSessionCredentials credentials = null;
            if (authSession instanceof AWSCognitoAuthSession) {
                AWSCognitoAuthSession cognitoAuthSession = (AWSCognitoAuthSession) authSession;
                com.amplifyframework.auth.AWSCredentials awsCredentials =
                        cognitoAuthSession.getAwsCredentialsResult().getValue();
                if (awsCredentials instanceof AWSTemporaryCredentials) {
                    AWSTemporaryCredentials temporaryAwsCredentials =
                            (AWSTemporaryCredentials) awsCredentials;
                    credentials = new BasicSessionCredentials(
                            temporaryAwsCredentials.getAccessKeyId(),
                            temporaryAwsCredentials.getSecretAccessKey(),
                            temporaryAwsCredentials.getSessionToken()
                    );
                }
            }

            if (credentials != null) {
                sdkCredentials.complete(credentials);
            } else {
                sdkCredentials.completeExceptionally(
                        new RuntimeException("Failed to get credentials")
                );
            }
        }, (exception) -> sdkCredentials.completeExceptionally(
                new RuntimeException("Failed to get credentials", exception)
        ));

        return sdkCredentials.join();
    }

    @Override
    public void refresh() {
        CompletableFuture<Void> result = new CompletableFuture<>();
        Amplify.Auth.fetchAuthSession(
                AuthFetchSessionOptions.builder().forceRefresh(true).build(),
                // We do not need to capture value if refresh succeeds
                (authSession) -> result.complete(null),
                // We do not need to throw if refresh fails
                (exception) -> result.complete(null)
        );

        result.join();
    }
}
```

</Block>
<Block name="Kotlin">

```kotlin
import com.amazonaws.auth.AWSCredentials
import com.amazonaws.auth.AWSCredentialsProvider
import com.amazonaws.auth.BasicSessionCredentials
import com.amplifyframework.auth.AWSTemporaryCredentials
import com.amplifyframework.auth.cognito.AWSCognitoAuthSession
import com.amplifyframework.auth.options.AuthFetchSessionOptions
import com.amplifyframework.core.Amplify
import java.lang.RuntimeException
import kotlin.coroutines.resume
import kotlin.coroutines.resumeWithException
import kotlin.coroutines.suspendCoroutine
import kotlinx.coroutines.runBlocking

class AmplifyCredentialsProvider : AWSCredentialsProvider {

    override fun getCredentials(): AWSCredentials = runBlocking {
        suspendCoroutine { continuation ->
            Amplify.Auth.fetchAuthSession(
                { authSession ->
                    val awsTemporaryCredentials = (authSession as? AWSCognitoAuthSession)
                        ?.awsCredentialsResult?.value as? AWSTemporaryCredentials

                    val sdkCredentials = awsTemporaryCredentials?.let {
                        BasicSessionCredentials(it.accessKeyId, it.secretAccessKey, it.sessionToken)
                    }

                    if (sdkCredentials != null) {
                        continuation.resume(sdkCredentials)
                    } else {
                        val authException = RuntimeException("Failed to get credentials")
                        continuation.resumeWithException(authException)
                    }
                },
                {
                    continuation.resumeWithException(
                        RuntimeException("Failed to get credentials. See exception.", it)
                    )
                }
            )
        }
    }

    override fun refresh() = runBlocking {
        suspendCoroutine { continuation ->
            Amplify.Auth.fetchAuthSession(
                AuthFetchSessionOptions.builder().forceRefresh(true).build(),
                // We do not need to capture value if refresh succeeds
                { continuation.resume(Unit) },
                // We do not need to throw if refresh fails
                { continuation.resume(Unit) }
            )
        }
    }
}
```

</Block>
</BlockSwitcher>

You can now use your `AmplifyCredentialsProvider` in any plugins that accept an `AWSCredentialsProvider`, instead of using `AWSMobileClient.getInstance()` as your AWSCredentialsProvider.

## Providing AWS Configuration Information

* `amplify_outputs.json`
  * used by Amplify v2
* `awsconfiguration.json`
  * used by AWS Android SDK  
* 👀if you are using Amplify v2 + AWS Android SDK -> sync `amplify_outputs.json` & `awsconfiguration.json`👀 
  * Amplify CLI generates and updates BOTH previous files
  * ⚠️MANUAL customizations should be applied | BOTH previous files⚠️ 

* TODO:
For AWS Android SDK plugins that require configuration information, you can continue to use the `AWSConfiguration` class.

<BlockSwitcher>
<Block name="Java">

```java
AWSConfiguration awsConfiguration = new AWSConfiguration(context);
```

</Block>
<Block name="Kotlin">

```kotlin
val awsConfiguration = AWSConfiguration(context)
```

</Block>
</BlockSwitcher>

## Example Usage of  AWS Android SDK Plugins with Amplify v2

This is not an exhaustive list of supported plugins. Any plugins that accept an AWSCredentialsProvider and do not rely on AWS Mobile Client should work.

### S3 Storage (com.amazonaws:aws-android-sdk-s3)

<BlockSwitcher>
<Block name="Java">

```java
AWSConfiguration awsConfiguration = new AWSConfiguration(context);
TransferUtility transferUtility = TransferUtility.builder()
    .context(context)
    .awsConfiguration(awsConfig)
    .s3Client(
        new AmazonS3Client(
            new AmplifyCredentialsProvider(),
            Region.getRegion(Regions.US_EAST_1)
        )
    )
    .build();
```

</Block>
<Block name="Kotlin">

```kotlin
val awsConfiguration = AWSConfiguration(context)
val transferUtility = TransferUtility.builder()
    .context(context)
    .awsConfiguration(awsConfiguration)
    .s3Client(
        AmazonS3Client(
            AmplifyCredentialsProvider(),
            Region.getRegion(Regions.US_EAST_1)
        )
    )
    .build()
```

</Block>
</BlockSwitcher>

### IoT (com.amazonaws:aws-android-sdk-iot)

<BlockSwitcher>
<Block name="Java">

```java
AWSIotClient client = new AWSIotClient(new AmplifyCredentialsProvider());

```

</Block>
<Block name="Kotlin">

```kotlin
val client = AWSIotClient(AmplifyCredentialsProvider())
```

</Block>
</BlockSwitcher>



### Android SDK Generated by API Gateway (aws-android-sdk-apigateway-core)

<BlockSwitcher>
<Block name="Java">

```java
ApiClientFactory clientFactory = new ApiClientFactory();
clientFactory.credentialsProvider(new AmplifyCredentialsProvider());
```

</Block>
<Block name="Kotlin">

```kotlin
val clientFactory = ApiClientFactory()
clientFactory.credentialsProvider(AmplifyCredentialsProvider())
```

</Block>
</BlockSwitcher>

</InlineFilter>
