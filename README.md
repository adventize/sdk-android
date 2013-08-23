#Adventize Android SDK integration

There are two types of SDK: `AdvertiserSDK` and `PublisherSDK`. The difference is that AdvertiserSDK contains only Advertiser's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertiser.

AdvertiserSDK helps us to understand when our client launches your application, so you pay only for the successful launch. Publishers SDK besides all above also allows you to integrate our offerwall into your application.
##Advertiser integration

Download Advertiser SDK — `adventize-advertiser-android-sdk.zip`.

Archive with AdventiserSDK contains `ExampleAdvertiser` project and `SDK_adv`  directory with `libAdvertiserSDK` JAR library and `deps` — dependencies directory with other JAR libraries.

To integrate AdventizerSDK in your app make following steps:

1. Add `libAdvertizeSDK.jar` to your project. Also add libs from `SDK_adv/deps` if you don't have them yet.
2. Add these permissions to your `AndroidManifest.xml`:

        ...
        <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
        ...

3. Import advertizer's class in your main class:

        import com.adventize.advertiser.Advertiser;

4. Start advertizer's session in your main class, when your application starts, passing Activity, id of your app in our system and your secret code, which you get on manager dashboard:

        Advertiser.startSession(this, "Your appId", "Your secret");

5. Stop advertizer's session before your application will terminate. It is recommended to use finish() function.

        Advertiser.stopSession();

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.
##Publisher integration
###Basic integration

Download PublisherSDK — `adventize-publisher-android-sdk.zip`.

Archive with PublisherSDK contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains adventize jar library and `deps` - dependencies directory.

To integrate PublisherSDK in your app follow these steps:

1. Add `libPublisherSDK.jar` to your project dependencies. Also add libs from `SDK_pub/deps` if you don't have them yet
2. Add these permissions to your AndroidManifest.xml:

        ...
        <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
        ...

3. Import Publisher class and banner's callback interface in class, where you want to call banners.

        import com.adventize.main.Publisher;
        import com.adventize.main.AskBannerCallback;

4. Use this to initialise sdk with your `appId` on start (before calling ad!):

        Publisher.startSession(getApplicationContext(), "Your appId"); 

5. Stop ad session before your application will terminate. It is recommended to use finish() function.

        Publisher.stopSession();

###Banner integration

1. Import banner's callback interface in class, where you want to initiate banners downloading:

        import com.adventize.main.AskBannerCallback;

2. Implement banner's callback interface in this class:

        public class YourClass implements AskBannerCallback ...

3. Call askBanners function, passing AskBannerCallback, Activity and String locations, that you mention in your dashboard.

        Publisher.askBanners(this, this, "banner1", "banner2");

4. Implement onBannersLoaded callback in your AskBannerCallback implementation. In this method you get a list of banners, which can be empty or null, if no banners exists.

        @Override
        public void onBannersLoaded(@Nonnull final List banners) {
        }
5. Use banners as normal WebViews. F.e. you can attach them to simple Dialog with LinearLayout:

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:orientation="vertical"
            android:focusableInTouchMode="false"
            android:id="@+id/bannerLayout"
            android:layout_width="200dp"
            android:layout_height="200dp"/>
6. Use banner's close listener to make close call when banner is closing, if you want.
To use close listener first import it in class, where you implement AskBannerCallback:
    
	    import com.adventize.banner.CloseListener;
And then use this example in onBannersLoaded to show banner in Dialog and to add CloseListener to it:

	    if (banners.isEmpty()) {
        return;
        }
        final Banner banner = banners.get(0);
        bannerDialog = new Dialog(this) {
        @Override
        protected void onCreate(final Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            this.setContentView(R.layout.banner_dialog);
            banner.setCloseListener(new CloseListener() {
                @Override
                public void onCloseBanner(final Banner banner) {
                    dismiss();
                }
            });
            ViewGroup l = (ViewGroup) findViewById(R.id.bannerLayout);
            l.addView(banner, new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
            }
        };
        bannerDialog.show();

To pass the integration you should make 3 ad requests. Just ask for banner 3 times. Do not worry if the banner will be empty. It is so because your app is not integrated and under moderation.

###Adding banner's custom callbacks.
If you want to specify custom callbacks on banner's actions do following:

1. Import CustomCallback class in class, where you want to keep callbacks

        import com.adventize.banner.CustomCallback;
2. Create CustomCallback object and save it as varaible. Create new Callable object and override `call` method. In this method you can place your code, which will be called, if banner makes mathching action.

        callback = new CustomCallback(new Callable&lt;Void&gt;() {
            @Override
            public Void call() throws Exception {
                exampleCustomCallback(callback);
                return null;
            }
        });
3. Add created callback to SDK, passing action name and callback. Action name is the name of banner's matching action for this callback.

        Publisher.addCallback("goToShop", callback);
4. You can get some params from callback. But first you should check if params are not null.

        Map<String, String> params = self.getParams();
        if(params != null)
            Log.d("Example","got callback with params : " + params.toString());
        else
            Log.d("Example","got callback without params.");

Note, that you can add multiple callbacks with different names.
##Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK. Follow publisher's instructions to integrate publisher and advertizer's instructions from 4 till last items to integrate advertizer. Use `Advertiser.startSession(this, "Your appId", "Your secret");` instead `Publisher.startSession(getApplicationContext(), "Your appId");` Use stop session of advertiser also.
