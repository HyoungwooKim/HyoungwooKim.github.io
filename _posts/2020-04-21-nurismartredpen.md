---
title:  "누리스마트빨간펜"
excerpt: ""
header:
  teaser: /assets/images/yoondongju-teaser.jpg

categories:
  - 프로젝트
tags:
  - 프로젝트
  - 취업
last_modified_at: 2020-04-24T08:06:00-05:00
---

### 매인 개발
### 주요 기능
* Bluetooth를 이용한 스마트펜 연결 기능 구현
``` c#
//펜 연결 표준화
if (!AndroidPenManager.Instance.IsPenConnected && !ConnectStatePopup.Instance.b_showConnectPopup && !PlayManager.Instance.isAdditiveSettingScene
    && !PlayerPrefsManager.GetBool (GlobalDefines.KEY_IS_CONNECT_CHECKPOPUP)) {
    if (AndroidPenManager.Instance.GetPairedPenList ().Length == 0) {
      ConnectStatePopup.Instance.b_showConnectPopup = true;
      PlayerPrefsManager.SetBool (GlobalDefines.KEY_IS_CONNECT_CHECKPOPUP, true);

      // 누리 스마트 빨간펜앱은 교원스마트펜이 연결되어 있어야 이용이 가능해요. 교원스마트펜을 연결할까요?
      PopupManager.ShowConnectPopup (LayerProduct.LocalizeData.GetString ("popup_pen_disconnected_title"),
        LayerProduct.LocalizeData.GetString ("popup_pen_disconnected_content"), (() => {
          startConnectPen ();
        }));
    } else if (PlayerPrefsManager.GetBool (GlobalDefines.KEY_IS_PEN_AUTOCONNECT) && !AndroidPenManager.Instance.b_isAutoConnecting
      && AndroidPenManager.Instance.IsPenConnected == false && !PlayerPrefsManager.GetBool (GlobalDefines.KEY_IS_CONNECT_CHECKPOPUP)) {
      if (!AndroidPenManager.Instance.GetLastConnectedPen ().Contains ("00:00:00:00:00:00")) {
        AndroidPenManager.Instance.StartAutoConnect ();
      }
    } 
} else if (PlayManager.Instance.isAdditiveSettingScene || !PlayerPrefsManager.GetBool (GlobalDefines.KEY_IS_PEN_AUTOCONNECT)) {
    if (AndroidPenManager.Instance.GetPairedPenList ().Length != 0 && AndroidPenManager.Instance.b_isAutoConnecting) {
      AndroidPenManager.Instance.StopAutoConnect ();
      AndroidPenManager.Instance.onCancelConnect ();
    }
}
```
* 서버 통신을 통해 LMS (Learning Manager System)을 구현
``` c#
/// <summary>
/// Completes the jindo.
/// </summary>
void CompleteJindo()
{
    InstantLoading.Show();

    Dictionary<string, string> headerDic = new Dictionary<string, string>();
    headerDic.Add("spenAuthToken", AppData.Instance.LoginToken);
    headerDic.Add("spenDeviceSN", AppData.Instance.DeviceId);

    Dictionary<string, string> valueDic = new Dictionary<string, string>();
    valueDic.Add("id", AppData.Instance.LoginId);
    valueDic.Add("step", step.ToString());
    valueDic.Add("ho", ho.ToString());

    Debug.LogWarning("jindo info : " + step.ToString() + " / " + ho.ToString());

    RequestHelper.requestPost(HttpUrl.BaseUrl + HttpUrl.API.JindoComplete, headerDic, valueDic, (JsonData jsonData)=>{
      InstantLoading.Hide();
      if (jsonData != null) {
        string resultCode = (string)jsonData["resultCode"];
        //0000	정상적으로 처리 되었습니다.
        //2001	이미 완료된 학습입니다.
        //9999	요청이 실패하였습니다.
        //1999	토큰이 유효하지 않습니다.
        //1986	다른 기기에서 로그인 중입니다.
        if (resultCode.Equals("0000")) {
          //Set json jindo complete.
          AppData.Instance.setCompleteStepHo(step, ho);
        } else{
          Debug.LogWarning("Jindo complete Failed");
        }
        Debug.Log("Jindo Complete resultCode : " + resultCode);
      } 
    });
}
```
* AVPro 에셋으로 비디오 스트리밍 기능 구현
``` c#
/// <summary>
/// Shows the video.
/// </summary>
/// <param name="_videoUrl">Video URL.</param>
/// <param name="_step">Step.</param>
/// <param name="_ho">Ho.</param>
/// <param name="_isFinal">If set to <c>true</c> is final.</param>
public static void ShowVideo(string _videoUrl, int _step, int _ho, bool _isFinal) 
{
    videoUrl = _videoUrl;
    step = _step;
    ho = _ho;
    isFinal = _isFinal;
    SoundManager.Instance.StopBGM();
    SceneLoader.Instance.LoadScene(GlobalDefines.SCENE_VIDEO);
}
```
* 교원 플랫폼 'ALLnG' sdk 연동 및 로그인 기능 구현
``` c#
/// <summary>
/// Gets the instance.
/// <para>  </para>
/// Will be null if runtime platform is not android
/// </summary>
/// <value>The instance.</value>
public static KyowonAPIBridge Instance {
    get {
        if (Application.platform != RuntimePlatform.Android)
            return null;
        if (!KyowonAPIBridge._instance) {
            GameObject apiObj = new GameObject ("_KyowonApiBridge");
            KyowonAPIBridge mInstance = apiObj.AddComponent<KyowonAPIBridge> ();
            _instance = mInstance;
            DontDestroyOnLoad (apiObj);
        }
        return _instance;
    }
}
/// <summary>
/// Checks the update.
/// <para>   </para>
///  callback - functionName(string result) : result = storeuri, "noupdate", "fail"
/// <para>   </para>
///  storeuri starts with allngstore://
/// </summary>
/// <param name="gameobjectName">Gameobject name.</param>
/// <param name="functionName">Function name. callback - functionName(string result) : result = storeuri, "noupdate", "fail"<para>   </para>
///  storeuri starts with allngstore://</param>
public void CheckUpdate (string gameobjectName, string functionName)
{
    AndroidJavaClass jc = new AndroidJavaClass ("com.unity3d.player.UnityPlayer"); 
    AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject> ("currentActivity");
    using (AndroidJavaClass jc2 = new AndroidJavaClass("com.layer.kyowonutils.KyowonUtils")) { 
        jc2.CallStatic ("checkUpdateVersion", jo, gameobjectName, functionName); 
  
    }
}
```
