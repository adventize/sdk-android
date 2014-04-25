#Adventize Android SDK integration

There are two types of SDK: `AdvertiserSDK` and `PublisherSDK`. The difference is that AdvertiserSDK contains only Advertiser's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertiser.

AdvertiserSDK helps us to understand when our client launches your application, so you pay only for the successful launch. Publishers SDK besides all above also allows you to integrate our offerwall into your application.
##Advertiser integration

Download Advertiser SDK — `adventize-advertiser-android-sdk.zip`.

Archive with AdventiserSDK contains `ExampleAdvertiser` project and `SDK_adv`  directory with `libAdvertiserSDK` JAR library and `deps` — dependencies directory with other JAR libraries.

To integrate AdventizerSDK in your app make following steps:

1. Add `libAdvertizeSDK.jar` to your project. Also add libs from `SDK_adv/deps` if you don't have them yet.  
**Important! Our libraty use Google Analytics lib V3. If you prefer Google Analytics V2 - just replace our dep with yours and tell sdk to use V2 then.**
2. Add these permissions to your `AndroidManifest.xml`:

        ...
        <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
        ...

3. Import advertizer's class in your main class:

        import com.adventize.sdk.Advertiser;
        
4. Create method to use Advertiser's SDK instance as singleton: 

        private static Advertiser sAdventize;

        public Advertiser getAdventize(final Context context) {
        if (sAdventize == null) {
            String yourAppId = "Your app id";
            String yourSecret = "some_secret";
            sAdventize = new Advertiser(context, yourAppId, yourSecret) {
                @Override
                protected RequestQueue getRequestQueue() {
                    return Volley.newRequestQueue(context);
                }
            };
        }

        return sAdventize;
        }
Pass your app's context, id of your app in our system, your secret code, which you get on manager dashboard, and version of google analytics lib to the Publisher's constructor.  
Note, that only second and third versions of google analytics are supported.
5. Start advertizer's session in your main class, when your application starts, using metod, you created in p.4:

        getAdventize(this).startSession();

6. Stop advertizer's session before your application will terminate. It is recommended to use `onDestroy()` function. 

        protected void onDestroy() {
        if (isFinishing()) {
            getAdventize(this).stopSession();
        }
        super.onDestroy();
        }

7. To identify your user you cat set your user's id and it will be reported in any Adventize action: `getAdventize(this).setUserID("Your user id");` You can set it multiple times, it is recommended to use it in login methods. 
8. To send actions your user done to server - use `getAdventize(this).sendAction("action");` Where action is an action which you set in Adventize dashboard.

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.
##Publisher integration
###Basic integration

Download PublisherSDK — `adventize-publisher-android-sdk.zip`.

Archive with PublisherSDK contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains adventize jar library and `deps` - dependencies directory.

To integrate PublisherSDK in your app follow these steps:

1. Add `libPublisherSDK.jar` to your project dependencies. Also add libs from `SDK_pub/deps` if you don't have them yet.  
**Important! Our libraty use Google Analytics lib V3. If you prefer Google Analytics V2 - just replace our dep with yours and tell sdk to use V2 then.**
2. Add these permissions to your AndroidManifest.xml:

        ...
        <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
        ...

3. Import Publisher class and responce callbacks in classes, from where you want to call ads. 

        import com.adventize.sdk.Publisher;
        import com.adventize.legasy.FetchResponse;

4. Create method to use Advertiser's SDK instance as singleton: 

        private static Publisher sAdventize;

        public Publisher getAdventize(final Context context) {
        if (sAdventize == null) {
            String yourAppId = "Your app id";
            sAdventize = new Publisher(context, yourAppId, null) {
                @Override
                protected RequestQueue getRequestQueue() {
                    return Volley.newRequestQueue(context);
                }
            };
        }

        return sAdventize;
        }
Pass your app's context, id of your app in our system, your secret code, which you get on manager dashboard, and version of google analytics lib to the Publisher's constructor.  
Note, that only second and third versions of google analytics are supported.
5. Start publisher's session in your main class, when your application starts, using metod, you created in p.4:

        getAdventize(this).startSession();

6. To ask for simple offerwall you should create anonymous class and override Response's onResponse method: 

        getAdventize(this).requestOffers(new Response<OfferWallItem[]>() {
        @Override
            public void onResponse(final ResponseStatus status, final OfferWallItem[] result) {
                onFetchResponse(status, result);
            }
        });
Where getAdventize is a method, which you created earlier,  
Response is Adventize interface at `com.adventize.sdk`,  
OfferWallItem is Adventize Ad item, at `com.adventize.offers`,  
ResponseStatus is Adventize enum, containing the result of response. It is situated at `com.adventize.sdk`,  
onFetchResponse is your method, where you handle the result of fetch response. 
7. To process offerwall response create method `onFetchResponse(ResponseStatus status, OfferWallItem[] request)`. If status is OK - there will be a result. If there was an error during the request - result will be null.  
Remember, that result array can be empty! 
8. After showing ad to your user you should collect all impressions (all ads, which were shown) and report it: 

        getAdventize(this).sendImpressions(impressions, impressions callback);
Where impressions is the list of ids of shown ads: `List<Long> impressions = new ArrayList<Long>();`,
And callback is the imprelentation of `com.adventize.legasy.SendImpressionsCallback`
9. In callback you should check the method's param. If it is not true - impressions weren't send. Than you should save them and try again later. 
10. To make click on ad item - just open Intent with item's url: 

        final Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setData(Uri.parse((OfferWallItem)clicked_ad_item.getUrl()));
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
It will open browser, if ad item targets site or android market app, if ad item targets an app.  
**Important! In case of targeting an app - market app should be installed on device!**
11. To identify your user you cat set your user's id and it will be reported in any Adventize action: `getAdventize(this).setUserID("Your user id");` You can set it multiple times, it is recommended to use it in login methods.
12. You can send to Adventize your user's rate to count reward for CPA with `getAdventize(this).setUserRate((long)rate);`
13. Stop publisher's session before your application will terminate. It is recommended to use `onDestroy()` function. 

        protected void onDestroy() {
        if (isFinishing()) {
            getAdventize(this).stopSession();
        }
        super.onDestroy();
        }


###Banner integration

1. Import banner's callback interface in class, where you want to initiate banners downloading:

        import com.adventize.banner.BannerResponse;

2. To ask for banner you should create anonymous class and override Response's onResponse method: 

        getAdventize(this).requestBanners(new Response<BannerEntity[]>() {
        @Override
            public void onResponse(ResponseStatus responseStatus, BannerEntity[] bannerEntities) {
                onBannersResponse(responseStatus, bannerEntities);
            }
        }, "firstBanner", "secondBanner", "NthBanner");
Where getAdventize is a method, which you created earlier,  
Response is Adventize interface at `com.adventize.sdk`,  
BannerEntity is Adventize banner item, at `com.adventize.banner`,  
ResponseStatus is Adventize enum, containing the result of response. It is situated at `com.adventize.sdk`,  
onBannersResponse is your method, where you handle the result of fetch response. 

3. To process banners response create method `onBannersResponse(ResponseStatus status, BannerRequest request)`. If status is OK - there will be a result. If there was an error during the request - result will be null.  
Remember, that result array can be empty! 

4. You can get banner's html by `(BannerEntity)banner.getBody()` and use it in your webView.

5. Or you can use Adventize banner (`com.adventize.banner.Banner`) class as normal WebView. F.e. you can attach them to simple Dialog with LinearLayout:

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:focusableInTouchMode="false"
        android:id="@+id/bannerLayout"
        android:layout_width="200dp"
        android:layout_height="200dp"/>

        Dialog bannerDialog = new Dialog((Application)this) {
            @Override
            protected void onCreate(final Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            this.setContentView(R.layout.banner_dialog);
    
            ViewGroup l = (ViewGroup) findViewById(R.id.bannerLayout);
            l.addView(Banner)banner, new LinearLayout.LayoutParams(
            ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
        }
        };
        bannerDialog.show();

6. Use banner's close listener (`com.adventize.banner.CloseListener`) to make close call callback when banner is closing, if you want.

        (Banner)banner.setCloseListener(new CloseListener() {
            @Override
                public void onCloseBanner(final Banner banner) {
                    dismiss();
                }
            });

To pass the integration you should make 3 ad requests. Just ask for banner 3 times. Do not worry if the banner will be empty. It is so because your app is not integrated and under moderation.

##Both publisher and advertizer
To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK and call Advertiser's methods on your Publisher instance. 

##Important!
If you plan to obfuscate your code - add these to your obfuscator configuration:

    -dontwarn com.adventize.analytics.Analytics_v2
    -dontwarn com.adventize.analytics.Analytics_v3