# Adventize Android SDK integration

There are two types of SDK: AdvertiserSDK and PublisherSDK. The difference is that AdvertiserSDK contains only Advertiser's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertiser.


## Advertiser integration

Download Advertiser SDK [adventize-advertiser-android-sdk.zip](https://github.com/adventize/sdk-android/raw/master/adventize-advertiser-android-sdk.zip).

Archive with AdventizerSDK contains `ExampleAdvertiser` project directory with all needed JAR libraries: libAdvertiserSDK and libGoogleAnalyticsV2, source file, where SDK is used and default resources.

To integrate AdventizerSDK in your app make following steps:

1. Add `libAdvertizeSDK.jar` to your project;
2. Add these permissions to your `AndroidManifest.xml`:

        ...
	    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
	    <uses-permission android:name="android.permission.INTERNET"/>
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
	    ...

3. Import advertiser's class in your main class:

		import com.adventize.advertiser.Advertiser;

4. Start adventize session in your main class, when your application starts:

		Advertiser.startSession(this, "Your appId", "Your secret");
		
	`appId` and `secret` you may find in details section of your app on Adventize.com

6. Stop adventize session before your application will terminate. It is recommended to use `finish()` function.

		Advertiser.stopSession();

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.


## Publisher integration

Download Publisher SDK [adventize-publisher-android-sdk.zip](https://github.com/adventize/sdk-android/raw/master/adventize-publisher-android-sdk.zip).

Archive with PublisherSDK contains `ExamplePublisher` project and adventize-sdk-module, where SDK is integrated, including all needed libs and resources.

To integrate PublisherSDK in your app follow these steps:

1. Create module `adventize-sdk-module`based on it's directory;
2. Add dependepcy of adventize-sdk-module in your main application module

3. Add these permissions to your `AndroidManifest.xml`:

    	...
	    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    	<uses-permission android:name="android.permission.INTERNET"/>
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
	    ...

3. Add Adventize activity & openUdid service to your <application> in AndroidManifest.xml:

	    <service android:name="com.adventize.openUdid.Adv_OpenUDID">
            <intent-filter>
                <action android:name="org.openudid.GETUDID" />
            </intent-filter>
        </service>

        <activity android:name="com.adventize.gui.MainActivity" android:label="@string/Offerwall" android:launchMode="singleTask"/>

4. Import main SDK class in class, where you want to show ad offerwall:

    	import import com.adventize.utils.AdventizeSDK;

5. Start publisher session when your application starts, setting appId of your application:

	    AdventizeSDK.startSession(getApplicationContext(), "Your appId");	    

6. Call offerwall window, sending your activity as a param:

	    AdventizeSDK.showOfferWallWindow(this);

6. Stop ad session before your application will terminate. It is recommended to use `finish()` function.

	    AdventizeSDK.stopSession();

To pass the integration you should make 3 ad fetches. Just ask for fetch 3 times. Do not warry if the fetch will be empty. It is so because your app is not integrated and under moderation.

## Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK. Follow publisher's instructions to integrate publisher and advertizer's instructions from 3 till last items to integrate advertizer.