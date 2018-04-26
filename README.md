
## Mobusi Mediation Unity

The current version (1.0.1) is compatible Unity 5, iOS 8(XCode 8) and above and android 15 and above. Containing Android SDK 1.14.1 and iOS SDK 2.3.6.

### ADD THE PACKAGE

Add the MobusiMediation.unitypackage by double clicking on it or by the unity menu Assets/Import package/Custom Package and select our package.

For iOS builds notice the mediation add a post-proccess to configure the xcode project properly to work with the mediation.

### IMPLEMENTATION

To start the mediation call the "InitWithAppId" method with your mediation app ID and the delegate that will receive the events. The "YOUR_API_HASH" is an id given in the mediation panel (<https://mediation.mobusi.com/login>). This will init the mediation and load the ads of all formats you have configured. The ads loads automatically so if you show an ad, automatically other is being loaded by the mediation.

        MobusiMediation.InitWithAppId ("YOUR_API_HASH", this);

You can enable or disable the mediation logs if you want more information or not:

        MobusiMediation.SetLogging (true);

To show an ad you have one method for each format:

        MobusiMediation.ShowBannerWithZone ("TAG_ZONE_IN_APP");

        MobusiMediation.ShowInterstitialWithZone ("TAG_ZONE_IN_APP");

        MobusiMediation.ShowVideoRewardedWithZone ("TAG_ZONE_IN_APP");

To check if an ad is ready to be shown :

        bool readyBanner = MobusiMediation.isBannerReady ();

        bool readyInterstitial = MobusiMediation.isInterstitialReady ();

        bool readyRewardedVideo = MobusiMediation.isRewardedVideoReady ();

### Mobusi Mediation Delegate Methods

Implement the delegate in your class :

        public class Home : MonoBehaviour, MobusiMediationDelegate

Add the methods :

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Home : MonoBehaviour, MobusiMediationDelegate
{
	Image banner;
	Image inter;
	Image rewarded;

	bool isBannerLoaded = false;
	bool isInterLoaded = false;
	bool isRewardedLoaded = false;

	private string appHash = "YOUR_API_HASH";

	void Start ()
	{
		banner = GameObject.Find ("bBanner").GetComponent<Image> ();
		inter = GameObject.Find ("bInter").GetComponent<Image> ();
		rewarded = GameObject.Find ("bRewarded").GetComponent<Image> ();

		MobusiMediation.InitWithAppId (appHash, this);
		MobusiMediation.SetLogging (true);
	}

	void Update ()
	{
		banner.enabled = isBannerLoaded;
		inter.enabled = isInterLoaded;
		rewarded.enabled = isRewardedLoaded;
	}

	public void PressButton (string button)
	{
		switch (button) {
		case "BANNER":
			Debug.Log ("Show banner");
			MobusiMediation.ShowBannerWithZone ("home");
			break;
		case "INTERSTITIAL":
			Debug.Log ("Show inter");
			MobusiMediation.ShowInterstitialWithZone ("home");
			AudioListener.pause = true;
			break;
		case "VIDEOREWARDED":
			Debug.Log ("Show rewardvideo");
			MobusiMediation.ShowVideoRewardedWithZone ("home");
			AudioListener.pause = true;
			break;
		}
	}



	public void OnMediationLoaded (MobusiMediation.AdTypes adType)
	{
		Debug.Log ("OnMediationLoaded " + adType);
		switch (adType) {
		case MobusiMediation.AdTypes.BANNER:
			isBannerLoaded = true;
			break;
		case MobusiMediation.AdTypes.INTERSTITIAL:
			isInterLoaded = true;
			break;
		case MobusiMediation.AdTypes.VIDEOREWARDED:
			isRewardedLoaded = true;
			break;
		}
		banner.enabled = false;
	}

	public void OnError (string description, MobusiMediation.AdTypes type, string zoneId)
	{
		Debug.Log (zoneId + " OnError " + description + " " + type);
	}

	public void OnClick (MobusiMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " OnClick");
	}

	public void OnClose (MobusiMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " OnClose");
	}

	public void OnShow (MobusiMediation.AdTypes adType, string provider, string zoneId)
	{
		AudioListener.pause = true;

		Debug.Log (zoneId + " OnShow");
	}

	public void OnReward (MobusiMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " onReward");
	}
}
```
