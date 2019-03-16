## Branch CLI for Automated iOS Integration

### Overview

The [Branch CLI](https://github.com/BranchMetrics/branch_io_cli) is a simple plugin which integrates Branch into your **iOS** project for you.

### Other Important Features:

* **Validates Universal Links**: The CLI ensures that your app supports Universal Links by comparing your project’s bundle identifier, team id, and domain entries in your entitlements file against what is stored in Branch’s Apple App Site Association file for your link domain.
* **Supports multiple targets**: To install for a specific target, use the [--target](https://github.com/BranchMetrics/branch_io_cli#options) flag, or use the configuration editor to change the inferred target each time. If the target in question is an extension, the tool will add the SDK, set the keys into the Info.plist, and patch source (if it's a Messages extension, for example).
* **Includes Rake for Task Management**: This gem has a rake for Task Management that is available [here](https://github.com/BranchMetrics/branch_io_cli#rake-task).
* **Fastlane Plugin**: There is also a Fastlane plugin available [here](https://github.com/BranchMetrics/branch_io_cli#using-fastlane), should you prefer to use it instead.

### Prerequisites:
1. Ensure that you’ve set up your Branch Dashboard using the instructions [here](/dashboard/integrate/), and have your Branch Key and Branch link domain handy.
2. An iOS project with which you’d like to integrate Branch.

### Installation
1. From terminal, cd into your iOS app’s project directory (the one that includes the xcodeproj file).
2. Type ```gem install branch_io_cli``` into terminal.

    **Note**: If you get an error saying that you don’t have permissions to perform this operation, try installing with sudo.

### Example: Using the CLI on a project with no third party SDKs

1. Type ```br setup``` into terminal, and you’ll be greeted with a few options on the following screen:

    ![image](/_assets/img/pages/resources/validation/cli_install_1.png)

2. Fill out your Branch Key from [Account Settings](https://branch.dashboard.branch.io/account-settings/app), and your app’s link domain information from your [Link Settings](https://branch.dashboard.branch.io/link-settings) pages.


    **Note**: You don’t need to add your -alternate.app.link domain. The CLI will take care of that for you.

3. Once complete, you’ll see the following set of instructions:

    ![image](/_assets/img/pages/resources/validation/cli_install_2.png)

4. If your app does not have any third party SDKs integrated, pick option 2: **Set this project up to use CocoaPods and add the Branch SDK**.
5. Before modifying your project, the CLI will confirm your inputs. If something is entered incorrectly, you’ll have the option to return and change your values:

    ![image](/_assets/img/pages/resources/validation/cli_install_3.png)

6. To make changes to your URI scheme for example, enter **N** to the prompt.
7. To insert your app’s URI scheme, enter **4** into the prompt:

    ![image](/_assets/img/pages/resources/validation/cli_install_4.png)
    ![image](/_assets/img/pages/resources/validation/cli_install_5.png)

8. After entering a URI scheme, enter option **19: Accept and Continue**, which starts the Universal Link validation process:

    ![image](/_assets/img/pages/resources/validation/cli_install_6.png)

9. If your link domains, are found in your app's **apple-app-site-association** file, the rest of the process will continue as expected.

    ![image](/_assets/img/pages/resources/validation/cli_install_7.png)

10. After installing cocoapods, the CLI will install the Branch plugin and inject values and various snippets from your app into your project.

    ![image](/_assets/img/pages/resources/validation/cli_install_8.png)

You're all done!

## AASA Validator

In case you need to validate your domain, use the [AASA Validator](https://branch.io/resources/aasa-validator/) to check universal linking is set up properly.

## App Indexing Validator

Check your app to see if it can properly index and surface content for Firebase through our [App Indexing Validator](https://branch.io/resources/app-indexing/).

## Universal Link Validator

### Overview

The Universal Link Validator compares an app’s local project settings to its dashboard settings and helps pinpoint errors that may prevent Universal Links from functioning properly. This service verifies that:

* the entries in the project’s .entitlements file are correct
* the entries in the project’s info.plist file are correct
* the .entitlements file has the correct build target selected
* the Apple App Prefix on the Dashboard matches the Apple Developer ID specified for the project

### Running the script

1. Download and extract the [script](https://branch.io/resources/universal-links/static/twigScript/ulv_script.sh)
1. Execute the script with the path to your .xcodeproj file

	```bash
	bash ulv_script.sh /Users/jbauer/Desktop/BranchStuff/Branch-TestBed-Swift/TestBed-Swift.xcodeproj`
	```

1. Upon execution, the script will return

	```bash
	#####################################################
	 Click the link generated to test your configuration
	#####################################################
	{"url":"https://ulv.app.link/RPyaHjpYJd"}
	```danielbrownk31

1. Clicking the generated link, or copying & pasting it into a browser, will open the validator and display the test results.
