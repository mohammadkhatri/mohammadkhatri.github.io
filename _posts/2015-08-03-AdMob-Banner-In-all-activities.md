---
layout: post
title: Admob Banner in all activities of android app.
description : Create Admob Banner in all activities of android app.
sitemap:
    priority: 0.9
    changefreq: 'weekly'
    lastmod: 2015-08-03T12:49:30-05:00
published: true
---

Creating [Admob Banner](https://developers.google.com/admob/android/banner?hl=en) is very easy to implement in android app.

1) create app in [Admob Console](https://apps.admob.com) and Get Admob unit id of banner.

2) use following in your layout xml file

```xml
<com.google.android.gms.ads.AdView
        android:id="@+id/adView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_alignParentBottom="true"
        ads:adSize="SMART_BANNER"
        ads:adUnitId="@string/banner_ad_unit_id">
</com.google.android.gms.ads.AdView>
```

3) use following to request ad in banner view in your activity.

```java
AdView mAdView = (AdView) findViewById(R.id.adView);
AdRequest adRequest = new AdRequest.Builder().build();
mAdView.loadAd(adRequest);
```

* For complete guide see [Admob integration Quickstart](https://developers.google.com/admob/android/quick-start).

**looks good , but what if your app is having so many activities and you want to put banner on all those.**

* possibe solutions

<p>1) create banner in all activity xml layouts and use above steps for all activities. <b>Doesn't seems impressive. huhhh...</b></p>

<p>1) Have some solution which bring banner working with less efforts. Let's try with <b>android.R.id.content</b></p>
<p>It's a root view [FrameLayout] of all activities in android app. So we can add banner to this view.</p>

* create ad layout file like this. [ad_layout.xml]

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:ads="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center" >

    <com.google.android.gms.ads.AdView
        android:id="@+id/adView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        ads:adSize="SMART_BANNER"
        ads:adUnitId="@string/banner1_ad_unit_id" >
    </com.google.android.gms.ads.AdView>

</LinearLayout>
```  

* Create Following method in your <b>BaseActivity</b> which is the base for all your activities.

```java
@SuppressLint("NewApi")
	protected void setupAdAtBottom() {
		content = (FrameLayout) findViewById(android.R.id.content);
		// inflate ad layout and set it to bottom by layouparams
		final LinearLayout ad = (LinearLayout) getLayoutInflater()
				.inflate(R.layout.ad_layout, null);
		FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(LayoutParams.MATCH_PARENT,
				LayoutParams.WRAP_CONTENT);
		params.gravity = Gravity.BOTTOM;
		ad.setLayoutParams(params);

		// adding viewtreeobserver to get height of ad layout , so that
		// android.R.id.content will set margin of that height
		ViewTreeObserver vto = ad.getViewTreeObserver();
		vto.addOnGlobalLayoutListener(new OnGlobalLayoutListener() {
			@Override
			public void onGlobalLayout() {
				if (Build.VERSION.SDK_INT < 16) {
					ad.getViewTreeObserver().removeGlobalOnLayoutListener(this);
				} else {
					ad.getViewTreeObserver().removeOnGlobalLayoutListener(this);
				}
				int width = ad.getMeasuredWidth();
				int height = ad.getMeasuredHeight();
				Log.i("ad hight", height + "");
				setSpaceForAd(height);

			}

		});
		addLayoutToContent(ad);

	}

	private void setSpaceForAd(int height) {

		// content.getChildAt(0).setPadding(0, 0, 0, 50);
		View child0 = content.getChildAt(0);
		FrameLayout.LayoutParams layoutparams = (android.widget.FrameLayout.LayoutParams) child0
				.getLayoutParams();
		layoutparams.bottomMargin = height;
		child0.setLayoutParams(layoutparams);

	}

	private void addLayoutToContent(View ad) {
		// content.addView(ad);
		content.addView(ad);
		AdView mAdView = (AdView) ad.findViewById(R.id.adView);
		AdRequest adRequest = new AdRequest.Builder().build();
		mAdView.loadAd(adRequest);
	}

```

* What this method **setupAdAtBottom** is doing is inflating ad_layout and add it to bottom of **android.R.id.content**.

* So now call setupAdAtBottom() after **setcontentView** of your activity. And Hulla... You are done. A single line will do the rest work and it will not affect your original layout.

* <a href="https://github.com/mohammadkhatri/AdMobApp" target="_blank">Download This demo project</a> for better understanding. 