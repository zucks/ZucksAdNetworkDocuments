<h1>ZucksAdNetworkSDK<br>
AdMobカスタムイベント導入手順</h1>

本手順書はAdMob カスタムイベント導入ガイドと合わせてご参照ください

AdMob カスタムイベント導入ガイド(Android)
https://developers.google.com/mobile-ads-sdk/docs/admob/android/custom-events?hl=ja
AdMob カスタムイベント導入ガイド(iOS)
https://developers.google.com/mobile-ads-sdk/docs/admob/ios/mediation?hl=ja#customevents

## 前提条件

ZucksAdNetworkSDKが導入済みであること
Google Play Services(Android)が導入済みであること
Google-Mobile-Ads-SDK(iOS)が導入済みであること

本実装では広告クリック数はAdMob管理画面には反映されません
(インプレッション数は連携されます)

## ステップ 1: カスタム イベントを定義する

AdMob管理画面にてカスタムイベントを作成し、下記項目を設定して下さい。

- ClassName : カスタムイベント実装クラス名を設定
- Label : AdMob管理画面上のレポートに表示される名称。任意の名称を設定
- Parameter : ZucksAdNetwork管理画面にて発行されるメディアIDを設定

※ ClassNameについて:
Android用のカスタムイベントの場合はクラス名をパッケージ名から指定します。
iOS用のカスタムイベントの場合はクラス名を指定します。

有効CPMの値を配信したい比率に合わせて設定して下さい。

<div style="page-break-before: always"></div>

##ステップ 2: カスタムイベント実装クラスを定義する

### Android

CustomEventBannerまたはCustomEventInterstitialを実装するクラスを定義します。
ステップ1のClass Nameで定義したクラス名と同一にしてください。

各実装例を下記に示します。
パッケージ名など必要に応じて変更してください。 

<div style="page-break-before: always"></div> 

### 実装例：バナー
```java
package com.your.packagename.sample;

import jp.co.voyagegroup.android.fluct.jar.FluctView;
import android.content.Context;
import android.os.Bundle;

import com.google.android.gms.ads.AdSize;
import com.google.android.gms.ads.mediation.MediationAdRequest;
import com.google.android.gms.ads.mediation.customevent.CustomEventBanner;
import com.google.android.gms.ads.mediation.customevent.CustomEventBannerListener;

public class ZucksCustomEventBanner implements CustomEventBanner {

    private FluctView mFluctView;

    @Override
    public void onDestroy() {
        if (mFluctView != null) {
            mFluctView.destroy();
        }
    }

    @Override
    public void onPause() {
    }

    @Override
    public void onResume() {
    }

    @Override
    public void requestBannerAd(Context context, CustomEventBannerListener listener, String serverParameter,
            AdSize size,
            MediationAdRequest mediationAdRequest, Bundle customEventExtras) {

        mFluctView = new FluctView(context, serverParameter);
        listener.onAdLoaded(mFluctView);
    }
}
```
<div style="page-break-before: always"></div>

### 実装例：インタースティシャル
```java
package com.your.packagename.sample;

import jp.co.voyagegroup.android.fluct.jar.FluctInterstitial;
import android.content.Context;
import android.os.Bundle;

import com.google.android.gms.ads.mediation.MediationAdRequest;
import com.google.android.gms.ads.mediation.customevent.CustomEventInterstitial;
import com.google.android.gms.ads.mediation.customevent.CustomEventInterstitialListener;

public class ZucksCustomEventInterstitial implements CustomEventInterstitial {

    private FluctInterstitial mFluctInterstitial;
    private FluctInterstitialCallback mInterstitialCallback = new FluctInterstitialCallback();
    private CustomEventInterstitialListener mCustomEventInterstitialListener;

    @Override
    public void onDestroy() {
    }

    @Override
    public void onPause() {
    }

    @Override
    public void onResume() {
    }

    @Override
    public void requestInterstitialAd(Context context, CustomEventInterstitialListener listener,
            String serverParameter,
            MediationAdRequest mediationAdRequest, Bundle customEventExtras) {
        mCustomEventInterstitialListener = listener;
        mFluctInterstitial = new FluctInterstitial(context, serverParameter);
        mFluctInterstitial.setFluctInterstitialCallback(mInterstitialCallback);
        mCustomEventInterstitialListener.onAdLoaded();

    }

    @Override
    public void showInterstitial() {
        mFluctInterstitial.showIntersitialAd();
    }

    private final class FluctInterstitialCallback implements FluctInterstitial.FluctInterstitialCallback {

        public void onReceiveAdInfo(int status) {
            switch (status) {
            case FluctInterstitial.STATUS_DISPLAY_DONE:
                mCustomEventInterstitialListener.onAdOpened();
                break;
            case FluctInterstitial.STATUS_AD_TAP:
                mCustomEventInterstitialListener.onAdClicked();
                mCustomEventInterstitialListener.onAdLeftApplication();
                break;
            case FluctInterstitial.STATUS_AD_CLOSE:
                mCustomEventInterstitialListener.onAdClosed();
                break;
            case FluctInterstitial.STATUS_AD_RATE_CANCEL:
                break;
            case FluctInterstitial.STATUS_NETWORK_ERROR:
            case FluctInterstitial.STATUS_MEDIA_ID_ERROR:
            case FluctInterstitial.STATUS_NONE_DATA:
            case FluctInterstitial.STATUS_AD_INFO_ERROR:
            case FluctInterstitial.STATUS_ANOTHER_ERROR:
            case FluctInterstitial.STATUS_SIZE_ERROR:
                mCustomEventInterstitialListener.onAdFailedToLoad(status);
                mCustomEventInterstitialListener.onAdClosed();
                break;
            default:
                mCustomEventInterstitialListener.onAdClosed();
                break;

            }
        }
    }
}
```

<div style="page-break-before: always"></div>

### iOS

GADCustomEventBannerまたはGADCustomEventInterstitialを実装するクラスを定義します。
ステップ1のClass Nameで定義したクラス名と同一にしてください。

各実装例を下記に示します。

### 実装例：バナー 
ヘッダ部

```objective-c
#import <Foundation/Foundation.h>
@import GoogleMobileAds;

@interface ZACustomEventBanner : NSObject<GADCustomEventBanner>
@end
```

実装部

```objective-c
#import "ZACustomEventBanner.h"
#import "FluctBannerView.h"

@implementation ZACustomEventBanner

@synthesize delegate = _delegate;

- (void)requestBannerAd:(GADAdSize)adSize
              parameter:(NSString *)serverParameter
                  label:(NSString *)serverLabel
                request:(GADCustomEventRequest *)request {

  FluctBannerView *bannerView = [[FluctBannerView alloc]
      initWithFrame:CGRectMake(0.0, 0.0, adSize.size.width,
                               adSize.size.height)];
  [bannerView setMediaID:serverParameter];

  [self.delegate customEventBanner:self didReceiveAd:bannerView];
}

@end
```
<div style="page-break-before: always"></div>
### 実装例：インタースティシャル
ヘッダ部

```objective-c
#import <Foundation/Foundation.h>
@import GoogleMobileAds;
#import "FluctInterstitialView.h"

@interface ZACustomEventInterstitial
    : NSObject<GADCustomEventInterstitial, FluctInterstitialViewDelegate>
@end
```
<div style="page-break-before: always"></div>
実装部

```objective-c
#import "ZACustomEventInterstitial.h"
#import "FluctInterstitialView.h"

@interface ZACustomEventInterstitial ()
@property(nonatomic) FluctInterstitialView *interstitialView;
@end

@implementation ZACustomEventInterstitial
@synthesize delegate = _delegate;
- (void)requestInterstitialAdWithParameter:(NSString *)serverParameter
                                     label:(NSString *)serverLabel
                                   request:(GADCustomEventRequest *)request {
  self.interstitialView = [[FluctInterstitialView alloc] init];
  self.interstitialView.delegate = self;
  [self.interstitialView setMediaID:serverParameter];

  dispatch_after(
      dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.0 * NSEC_PER_SEC)),
      dispatch_get_main_queue(),
      ^{ [self.delegate customEventInterstitialDidReceiveAd:self]; });
}

- (void)presentFromRootViewController:(UIViewController *)rootViewController {
  [self.interstitialView showInterstitialAd];
}

#pragma FluctInterstitialViewDelegate
- (void)fluctInterstitialView:(FluctInterstitialView *)interstitialView
callbackValue:(NSInteger)callbackValue {
  switch (callbackValue) {
    case FluctInterstitialShow:
      [self.delegate customEventInterstitialWillPresent:self];
      break;
    case FluctInterstitialTap:
      [self.delegate customEventInterstitialWasClicked:self];
      [self.delegate customEventInterstitialWillLeaveApplication:self];
      break;
    case FluctInterstitialClose:
      [self.delegate customEventInterstitialDidDismiss:self];
      break;
    default:
      break;
  }
}
@end
```



