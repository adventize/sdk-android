
# Adventize Android SDK integration

There are two types of SDK: AdvertiserSDK and PublisherSDK. The difference is that AdvertiserSDK contains only Advertiser's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertiser.


## Advertiser integration

Download AdvertiserSDK — [adventize-advertiser-android-sdk.tar.gz](https://github.com/adventize/sdk-android/raw/master/adventize-advertiser-android-sdk.tar.gz).

Archive with AdventizerSDK contains `ExampleAdvertiser` project for Intellij IDEA and `SDK_adv` directory with JAR library.

To integrate AdventizerSDK in your app make following steps:

1. Add `libAdvertizeSDK.jar` to your project;
2. Add these permissions to your `AndroidManifest.xml`:

    	...
	    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
	    <uses-permission android:name="android.permission.INTERNET"/>
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
	    ...

3. Import advertizer's class in your main class:

		import com.adventize.advertizer.Advertizer;

4. Start adventize session in your main class, when your application starts:

		Advertizer.startSession(this, "Your appId", "Your secret");
		
	`appId` and `secret` you may find in details section of your app on Adventize.com

6. Stop adventize session before your application will terminate. It is recommended to use `finish()` function.

		Advertizer.stopSession();

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.


## Publisher integration

Download PublisherSDK — [adventize-publisher-android-sdk.tar.gz](https://github.com/adventize/sdk-android/raw/master/adventize-publisher-android-sdk.tar.gz).

Archive with PublisherSDK also contains `ExamplePublisher` project for Intellij IDEA and `SDK_pub` directory. SDK directory contains JAR library with SDK and `res.zip` — archive with files needed for publisher's offerwall.

To integrate PublisherSDK in your app follow these steps:

1. Add `libPublisherSDK.jar` to your project;
2. Add these permissions to your `AndroidManifest.xml`:

    	...
	<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    	<uses-permission android:name="android.permission.INTERNET"/>
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
	...

3. Add Adventize activity to your <application> in AndroidManifest.xml:

	<activity android:name="com.adventize.publisher.main.MainActivity"
	android:label="@string/Offerwall"/>

4. Import main SDK class in class, where you want to show ad offerwall:

    	import com.adventize.publisher.main.Publisher;

5. Call offerwall window with your appId and your application Activity:

	Publisher.callOfferwall("Your appId",this);

6. Stop ad session before your application will terminate. It is recommended to use `finish()` function.

	Publisher.stopSession();

You can use adventize prefetch mechanizm to show fetch only when it is ready.</p>
To do so, use these steps:

1. Add <code>libPublisherSDK.jar</code> to your project;
2. Add these permissions to your <code>AndroidManifest.xml:
	...
	<uses-permission android:name="android.permission.READ_PHONE_STATE"/>;
	<uses-permission android:name="android.permission.INTERNET"/>;
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>;
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>;
	...
3. add Adventize activity to your <application> in AndroidManifest.xml:

	<activity android:name="com.adventize.publisher.main.MainActivity"
	android:label="@string/Offerwall"/>

4. Import main SDK class in your mian class:
	import com.adventize.publisher.main.Publisher;</code></pre>
	
5.Report about starting session when you start your app:

	Publisher.startSession(getApplicationContext());

6. Import SDK callback interface in class, where you want to get callbacks

	import com.adventize.publisher.publisher.AskFetchCallback;

7. Implement AskFetchCallback interface:
	public class myObj implements AskFetchCallback

8. Import main SDK class in class, where you want to show ad offerwall:

	import com.adventize.publisher.main.Publisher;

9. Use askFetch(AskFetchCallback obj, String appId, Activity context) to ask for prefetch.

	Publisher.askFetch(this, "Your appId", this);
	
10. implement fetchFinished to callOfferwall when ok is true:

	public void fetchFinished(boolean ok) {
        if(ok)
        {
            Publisher.callOfferwall("Your appId",this);
	}
}

11. Stop ad session before your application will terminate. It is recommended to use finish() function.

	Publisher.stopSession();

To pass the integration you should make 3 ad fetches. Just ask for fetch 3 times. Do not warry if the fetch will be empty. It is so because your app is not integrated and under moderation.

## Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK. Follow publisher's instructions to integrate publisher and advertizer's instructions from 3 till last items to integrate advertizer.