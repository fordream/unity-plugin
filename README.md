GreedyGame Unity Integration Guide
===================

This is a complete guide for integrating GreedyGame plugin within your unity game. You can download [GreedyGame_v7.4.4.unitypackage](current-sdk/GreedyGame_v7.4.4.unitypackage).


## Setting up
* In **panel.greedygame.com**
 1. Make account on panel.greedygame.com
 2. Create game profile to get **GAME_PROFILE_ID**
* In **UnityEngine**
 1. Import GreedyGame_vX.Y.Z.package into your unity project
 2. Goto, Top Menu > GreedyGame > AdUnitManager, use panel.greedygame.com's creditiional  to login
 3. Assign **GAME_PROFILE_ID**, select LoadingLevel and save

## Declaration of AdUnits
### Native Ads
![SharedAdUnit MonoBehaviour](https://raw.githubusercontent.com/GreedyGame/Unity-Sample/master/screen-shots/1_branded_game.png?raw=true "SharedAdUnit MonoBehaviour attached to Stockcar/Body_Complete" )

#### 1. Select GameObject for branding
* Attach compiled MonoBehaviour **AdUnit** or **SharedAdUnit**  to GameObject having **Renderer**.
* Supported Renderers are Mesh, Plan, Cloth and Sprite (only with SharedAdUnit).
* GameObject must having 2D texture.

> Preview: SharedAdUnit MonoBehaviour attached to Stockcar/Body_Complete

> ![SharedAdUnit MonoBehaviour](https://raw.githubusercontent.com/GreedyGame/Unity-Sample/master/screen-shots/2_attached_monobehaviour.png?raw=true "SharedAdUnit MonoBehaviour attached to Stockcar/Body_Complete" )
 1. SharedAdUnit Attached, yellow helpbox states it ready to build in unitlist
 2. 2D texture, will be used for branded assets, such as logo, product image etc.
 3. MeshRender will be used as renderer to blend branding image over object

#### 2. Setting up with Server
* Using TopMenu: *GreedyGame > DynamicUnitManager*
* Login using panel's credential.
* Build and sync unit list.

> Preview: list of units to be used for branding. Left list post refresh action and right list after save action.

> ![Refresh UnitList](https://raw.githubusercontent.com/GreedyGame/Unity-Sample/master/screen-shots/5_refresh_save.png?raw=true "list of units to be used for branding" )
 1. **GameProfileId**, game-id from panel.greedygame.com
 2. **LoadingLevel**, will be used for fetching and loading campaign assets
 3. **Save**, will upload images to server and create GlobalConfig objects at LoadingLevel

#### 3. Manage campagin fetching and post loading scene
* Attach sample script `GreedyCampaignLoader.cs` with loading scene's object.

### Floating Ad-Head

![SharedAdUnit MonoBehaviour](https://raw.githubusercontent.com/GreedyGame/Unity-Sample/master/screen-shots/7_float_ad.png?raw=true "SharedAdUnit MonoBehaviour attached to Stockcar/Body_Complete" )

* Attach `AdHeadLoader.cs` with respective scene's object to fetch floating AdHead on that scene.
  * Set-up ad-unit manually on panel.greedygame.com/games/**GAMEPROFILE_ID**/units 
  * AdUnit : Panel's ad-unit to fetch specific unit from server. 
```csharp
private GreedyAdManager ggAdManager = null;
void Awake (){
  ggAdManager = GreedyAdManager.Instance;
}

void Start (){
    //Fetch FloatAdLayout
    ggAdManager.FetchAdHead ("float-123");
}

void OnDestroy (){
    //Remove FloatAdLayout
    ggAdManager.RemoveAllAdHead ();
}
```

---

## Advance Customization Documentation

### GreedyAdManager
#### Class Overview
Contains high-level classes encapsulating the overall GreedyGame ad flow and model.

#### GreedyGame's headers 
```csharp
using GreedyGame.Runtime.Common;
using GreedyGame.Platform;
```
---

#### Public Singleton Constructors
`GreedyAdManager.Instance`

*Example*
```csharp
private GreedyAdManager ggAdManager = null;
void Awake(){
//Initialization as singleton
  ggAdManager = GreedyAdManager.Instance;
}
```
---

#### Init
`init(String GameId, String[] AdUnits, Boolean isDebug, Boolean isLazyLoad, OnGreedyEvent)`

Lookup for new native campaign from server.
* GameId - Unique game profile id from panel.greedygame.com
* AdUnits - Array register unit id. eg. Unit-XYZ
* isDebug- To build debug app for testing
* isLazyLoad- In case of true, it will show branded assets as soon as downloaded 
* OnGreedyEvent - Callback function for **RuntimeEvent** as follow:
  - CAMPAIGN_NOT_LOADED
  - CAMPAIGN_LOADED
  - CAMPAIGN_DOWNLOADED

*Example*
```csharp
void Start() {
  if (isSupported) {
    GlobalConfig[] ggLoaders = Resources.FindObjectsOfTypeAll<GlobalConfig> ();
    if(ggLoaders != null && ggLoaders.Length != 1){
      isSupported = false;
      Debug.LogError("None or multuple occurrence of GlobalConfig object found!");
      return;
    }
    GlobalConfig ggConfig = ggLoaders [0];
    ggAdManager.init (ggConfig.GameId, ggConfig.AdUnits.ToArray (), ggConfig.isDebug, ggConfig.isLazyLoad, OnGreedyEvent);
  }
}
```
---

#### OnGreedyEvent
`void OnGreedyEvent(RuntimeEvent greedy_events)`

Callback function for **RuntimeEvent**

*Example*
```csharp
void OnGreedyEvent(RuntimeEvent greedy_events){
  if (greedy_events == RuntimeEvent.CAMPAIGN_LOADED || 
      greedy_events == RuntimeEvent.CAMPAIGN_NOT_LOADED) {
  //Goto play scene if server reponse is recevied
    Application.LoadLevel (PostLevel);
  }
}
```
---

## Checking runtime unit list

1. Goto loading scene, here Demo Scene.
2. Select **GreedyGameConfigObject**, and look for **GlobalConfig** component attached.
3. Validate value of **GlobalConfig** component, with values from *panel.greedygame.com*

> ![GreedyGameConfigObject](https://raw.githubusercontent.com/GreedyGame/Unity-Sample/master/screen-shots/6_global_config.png?raw=true "Checking runtime unit list" )


---

## Android Setup
1. [For users running a version of Unity earlier than 5.0] Navigate to Temp/StagingArea of your project directory and copy AndroidManifest.xml to Assets/Plugins/Android. Add the following <meta-data> tag to the AndroidManifest.xml file:
  
  ```xml
  <activity android:name="com.unity3d.player.UnityPlayerActivity" ...>
    ...
    <meta-data android:name="unityplayer.ForwardNativeEventsToDalvik" android:value="true" />
  </activity>
  ```
2. And set application *hardwareAccelerated=true* as
  ```xml
  <application 
     android:hardwareAccelerated="true"
  ....
  </application>
  
  ```
