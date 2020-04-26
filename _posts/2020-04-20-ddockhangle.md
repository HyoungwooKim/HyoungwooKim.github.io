---
title:  "똑똑한글단추"
excerpt: ""
categories:
  - 프로젝트
tags:
  - 프로젝트
  - 취업
last_modified_at: 2020-04-24T08:06:00-05:00
---

### 매인 개발
### 주요 기능
* Vuforia sdk를 이용한 전집 책 표지 인식 AR기능 구현
``` c#
protected virtual void OnTrackingFound()
{
    if (mTrackableBehaviour)
    {
        string temp = "SCENE_" + this.transform.gameObject.name;
        SceneManager.Instance.LoadLevelAsync(GlobalDefines.GetPropValue(temp));
    }
}
```
* HOTween을 이용한 애니메이션 구현
``` c#
public void StartScene ()
{
    // Scene이 시작되면 각 bottombar, home button, 그리고 text들의 위치를 정렬 시킨다. ( 연출 효과 )
    StartCoroutine ("StartSceneCo");
}
IEnumerator StartSceneCo ()
{
    btnHome.transform.DOLocalMoveX(-120f, 0.3f).SetEase(Ease.OutBack).SetRelative();
    yield return new WaitForSeconds(0.15f);

    bottomBar.transform.DOLocalMoveY(257f, 0.3f).SetEase(Ease.OutBack).SetRelative();
    IconSclae ();
}
void IconSclae ()
{
    for(int i = 0; i < icons.Length; i++)
    {
        texts[i].DOLocalMove(startPosition[i],0.5f).SetEase(Ease.OutBack);
    }
}
```
* 카메라 및 갤러리 기능 네이티브 구현
``` c#
public void imageFromCamera(String callBackGameObjectName, String fileName) {
    mCallBackGameObjectName = callBackGameObjectName;
    Intent cameraIntent = new Intent(unityActivity, camera.class);
    unityActivity.startActivity(cameraIntent);
}
public void imageFromLibrary(String callBackGameObjectName) {

    mCallBackGameObjectName = callBackGameObjectName;
    Intent libraryIntent = new Intent(unityActivity, gallery.class);
    unityActivity.startActivity(libraryIntent);
}
```  
