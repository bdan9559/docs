# Branch Search SDK
The Branch Search SDK is an Android SDK provided by Branch Metrics Inc. to support mobile application content discovery. This SDK enables applications with the ability to search for contextual application content through the submission of user queries covering context and intent.


___


## Get the Demo App
BranchSearchDemo application added to this repo is a good example of how to integrate the Branch Search SDK with any application
# Getting Started


## Installation

**The compiled Branch Search SDK footprint is approximately 40kb**

### Install Branch Search SDK

Copy the BranchSearchSDK.aar file to your `project/libs` folder. Just add the following to your `build.gradle` dependency list
```gradle
    compile(name:'branchsearchsdk', ext:'aar')    
```


#### Additional dependencies and permissions
##### Permissions
1) Internet access permissions are required in order to request and return search results
2) Location permission (*optional*). When enabled, location will greatly enhance search results and is required in order to display local results

The Branch Search SDK uses Google Play services to access advertising IDs and location. To do so, add the following to your `build.gradle` dependency list
```gradle
    implementation 'com.google.android.gms:play-services-ads:' + PLAY_SERVICE_VERSION
    implementation 'com.google.android.gms:play-services-location:' + PLAY_SERVICE_VERSION
```

### Register Your App

You will need to register your app on the Branch dashboard: [https://dashboard.branch.io](https://dashboard.branch.io)

#### Add your Branch key to your project.

After you register your app, your Branch key can be retrieved on the [Settings](https://dashboard.branch.io/#/settings) page of the dashboard. Add it (them, if you want to do it for both your live and test apps) to your project's manifest file as a meta data.

 Edit your manifest file to include the above items
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="io.branch.sample"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-permission android:name="android.permission.INTERNET" />

    <application>
        <!-- Other existing entries -->

        <!-- Add this meta-data below, and change "key_live_xxxxxxx" to your actual live Branch key -->
        <meta-data android:name="io.branch.sdk.BranchKey" android:value="key_live_xxxxxxx" />

        <!-- For your test app, if you have one; Again, use your actual test Branch key -->
        <meta-data android:name="io.branch.sdk.BranchKey.test" android:value="key_test_yyyyyyy" />
    </application>
</manifest>
```

# Integrating Branch Search SDK

## Initialization
The Branch Search SDK needs to be initialized before using any search functionality. The SDK can be initialized anywhere within your application. Ideally should be from the `onCreate()` method of activity handling search
```java
     BranchSearch.init(activity, new IBranchInitEvents() {
         @Override
         public void onOptionalLocationPermissionMissing() {
             // Location permission is optional but provides better search results if available. Please get location permission from user.
             // Please Notify SDK with `BranchSearch.onLocationPermissionGranted()` when location permission granted by user
             // Ignore if don't want to provide location permissions
         }
     });
```


#### Search for apps and contents
Use `BranchSearchRequest` class to search for apps and content with Branch.
```java
    new BranchSearchRequest(keyword).search(context, new IBranchSearchEvents() {
        @Override
        public void onBranchSearchResult(BranchSearchResult branchSearchResult) {
            // Update UI with search results. BranchSearchResult contains the result of any search.
            branchSearchController.onBranchSearchResult(branchSearchResult);
        }
        @Override
        public void onBranchSearchError(BranchSearchError error) {
            // Handle any search errors here
        }
    });
```
`BranchSearchRequest` is a builder and allows many other attributes to be added to the query, such as limiting number of results per app or maximum number of results.
Search results are always updated through the `onBranchSearchResult(BranchSearchResult branchSearchResult)` callback.

#### Location permission handling
Branch Search SDK will provide better results if it can read the current location. For example, a user searching for a restaurant will receive location specific results if location permissions are enabled. The SDK will callback with `onLocationPermissionMissing` when there is no permission to read the location.
The application give location permissions if the user has enabled access, or choose to ignore location permissions entirely if unavailable. Please call the following method to notify if location permission is granted after SDK Initialization:
```java
    BranchSearch.onLocationPermissionGranted(activity);
```

## BranchSearchResult Class
BranchSearchResult class represents the search results for a BranchSearchRequest object. BranchSearchResult class contains the following

**BranchSearchRequest** The original search request that BranchSearchResult contains.
**Application Result List** A list of `BranchAppResult` objects representing the application results matched for the search request.
**Content Result List** A list of `BranchContentResult` object representing the contents from applications that matched the search request.

## BranchAppAction class
BranchAppAction class represents an action that can be completed by an application. Example actions are `call`, `deliver` etc. BranchAppAction provide the deeplinking to the content or action.
Please use `BranchAppAction#completeAction()` methods to complete the action.
