---
title: Setup Options for the SDK
---

# Setup Options for the SDK

The AWS SDK contains [high level client interfaces](./start) for quickly adding common features and functionality to your app. You can also manually add the generated AWS service interfaces for direct interaction if you have custom or advanced requirements.

## CocoaPods setup

The AWS Mobile SDK for iOS is available through [CocoaPods](https://cocoapods.org). Install CocoaPods by running the following commands from the folder containing your projects `*.xcodeproj` file:

```bash
gem install cocoapods
pod setup
pod init
```

In your project directory (the directory where your `*.xcodeproj` file is), open the empty text file named `Podfile`. Replace `myAppName` with your app name. You can also remove pods for services that you don't use. For example, if you don't use `AWSAutoScaling`, remove or do not include the `AWSAutoScaling` pod.

```ruby
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '9.0'
use_frameworks!

target :'YOUR-APP-NAME' do
    pod 'AWSAuth'
    pod 'AWSAuthCore'
    pod 'AWSAuthUI'
    pod 'AWSAutoScaling'
    pod 'AWSCloudWatch'
    pod 'AWSCognito'
    pod 'AWSCognitoAuth'
    pod 'AWSCognitoIdentityProvider'
    pod 'AWSCognitoIdentityProviderASF'
    pod 'AWSCore'
    pod 'AWSDynamoDB'
    pod 'AWSEC2'
    pod 'AWSElasticLoadBalancing'
    pod 'AWSFacebookSignIn'
    pod 'AWSGoogleSignIn'
    pod 'AWSIoT'
    pod 'AWSKMS'
    pod 'AWSKinesis'
    pod 'AWSLambda'
    pod 'AWSLex'
    pod 'AWSLogs'
    pod 'AWSMachineLearning'
    pod 'AWSMobileClient'
    pod 'AWSPinpoint'
    pod 'AWSPolly'
    pod 'AWSRekognition'
    pod 'AWSS3'
    pod 'AWSSES'
    pod 'AWSSNS'
    pod 'AWSSQS'
    pod 'AWSSimpleDB'
    pod 'AWSUserPoolsSignIn'
end
```

Once complete, run `pod install` and open the `*.xcworkspace` with Xcode and **build** your project to start using the SDK. Once you have created a workspace, always use `*.xcworkspace` to open the project instead of `*.xcodeproj`.

Whenever a new version of the SDK is released you can update by running `pod update` and rebuilding your project to use the new features.

## Carthage setup

The AWS Mobile SDK for iOS is available through [https://github.com/Carthage/Carthage](https://cocoapods.org). Install the latest version of [Carthage](https://github.com/Carthage/Carthage#installing-carthage).

Add the following to your `Cartfile`:

```bash
github "aws-amplify/aws-sdk-ios"
```

Once complete, run `carthage update` and open the `*.xcworkspace` with Xcode and chose your `Target`. In the `General` tab, find `Embedded Binaries`, then choose the `+` button.

Choose the `Add Other` button, navigate to the `AWS<#ServiceName#>.framework` files under `Carthage > Build > iOS` and select `AWSCore.framework` and the other service frameworks you require. Do not select the `Destination: Copy items` if needed check box when prompted.

* AWSAuth
* AWSAuthCore
* AWSAuthUI
* AWSAutoScaling
* AWSCloudWatch
* AWSCognito
* AWSCognitoAuth
* AWSCognitoIdentityProvider
* AWSCognitoIdentityProviderASF
* AWSCore
* AWSDynamoDB
* AWSEC2
* AWSElasticLoadBalancing
* AWSFacebookSignIn
* AWSGoogleSignIn
* AWSIoT
* AWSKMS
* AWSKinesis
* AWSLambda
* AWSLex
* AWSLogs
* AWSMachineLearning
* AWSMobileClient
* AWSPinpoint
* AWSPolly
* AWSRekognition
* AWSS3
* AWSSES
* AWSSNS
* AWSSQS
* AWSSimpleDB
* AWSUserPoolsSignIn

Under the `Build Phases` tab in your `Target`, choose the `+` button on the top left and then select `New Run Script Phase`.

Setup the build phase as follows. Make sure this phase is below the Embed Frameworks phase.

```bash
Shell /bin/sh

bash "${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}/AWSCore.framework/strip-frameworks.sh"

Show environment variables in build log: Checked
Run script only when installing: Not checked

Input Files: Empty
Output Files: Empty
```

Now **build** your project to start using the SDK. Whenever a new version of the SDK is released you can update by running `carthage update` and rebuilding your project to use the new features.

## Direct AWS Service access

You can call AWS service interface objects directly via the generated SDK clients. You can use the client credentials provided by the [AWSMobileClient](./authentication) when using the `.default()` method on a service object (e.g. `AWSSQS.default()`). This will leverage short term AWS credentials from Cognito Identity. Alternatively, you can call the constructors manually.

To work with service interface objects, your Amazon Cognito users' [IAM role](https://docs.aws.amazon.com/cognito/latest/developerguide/iam-roles.html) must have the appropriate permissions to call the requested services.
{: .callout .callout--warning}

For example, if you were using [Amazon Simple Queue Service (SQS)](https://aws.amazon.com/sqs/) in Swift you would first add `AWSSQS` to your `Podfile` and install the dependencies with `pod install`. Next, update your `awsconfiguration.json` like so:

```json
    "SQS" : {
        "Default": {
            "Region": "XX-XXXX-X"
        }
    }
```

**Note**: The key is `SQS` and not `AWSSQS` as the `awsconfiguration.json` file does not prefix keys with `AWS`.

Next, import `AWSSQS` in your Xcode project and create the client:

```swift
import AWSSQS

func addItemSQS() {
        let sqs = AWSSQS.default()
        let req = AWSSQSSendMessageRequest()
        req?.queueUrl = "https://sqs.XX-XXXX-X.amazonaws.com/XXXXXXXXXXXX/MyQueue"
        req?.messageBody = "hello world"
        sqs.sendMessage(req!) { (result, err) in
            if let result = result {
                print("SQS result: \(result)")
            }
            if let err = err {
                print("SQS error: \(err)")
            }
        }
    }
```

You could then call `self.addItemSQS()` to invoke this action from your app.

## Logging

As of version 2.5.4 of this SDK, logging utilizes [CocoaLumberjack SDK](https://github.com/CocoaLumberjack/CocoaLumberjack), a flexible, fast, open source logging framework. It supports many capabilities including the ability to set logging level per output target, for instance, concise messages logged to the console and verbose messages to a log file.

CocoaLumberjack logging levels are additive such that when the level is set to verbose, all messages from the levels below verbose are logged. It is also possible to set custom logging to meet your needs. For more information, see [CocoaLumberjack Logging Levels](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/CustomLogLevels.md).

You can change the logging level to suit the phase of your development cycle by importing AWSCore and calling:

```swift
AWSDDLog.sharedInstance.logLevel = .verbose
```

The following logging level options are available:

- `.off`
- `.error`
- `.warning`
- `.info`
- `.debug`
- `.verbose`

We recommend setting the log level to  `.off` before publishing to the App Store.

CocoaLumberjack can direct logs to file or used as a framework that integrates with the Xcode console. For example:

```swift
//File Logger example
let fileLogger: AWSDDFileLogger = AWSDDFileLogger() // File Logger
fileLogger.rollingFrequency = TimeInterval(60*60*24)  // 24 hours
fileLogger.logFileManager.maximumNumberOfLogFiles = 7
AWSDDLog.add(fileLogger)


//Console example
AWSDDLog.add(AWSDDTTYLogger.sharedInstance) // TTY = Xcode console
```

## DocSet for Xcode

Open the macOS terminal and go to the directory containing the expanded archive. For example:

```bash
$ cd ~/Downloads/aws-ios-sdk-2.7.0
```

**Note**: Replace 2.7.0 in the preceding example with the version number of the AWS Mobile SDK for iOS that you downloaded.

Create a directory called `~/Library/Developer/Shared/Documentation/DocSets`:

```bash
$ mkdir -p ~/Library/Developer/Shared/Documentation/DocSets
```

Copy (or move) `documentation/com.amazon.aws.ios.docset` from the SDK installation files to the directory you created in the previous step:

```bash
$ mv documentation/com.amazon.aws.ios.docset ~/Library/Developer/Shared/Documentation/DocSets/
```

If Xcode was running during this procedure, restart Xcode. To browse the documentation, go to **Help**, click **Documentation and API Reference**, and select **AWS Mobile SDK for iOS v2.7 Documentation** (where '2.7' is the appropriate version number).