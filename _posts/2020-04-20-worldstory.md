---
title:  "호야토야 세계옛이야기"
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
void MoveScene()
{
    string[] numString = null;

    numString = mTrackableBehaviour.TrackableName.Split('_');

    if (numString[1].Contains("item"))
    {
        SceneLoader.Instance.LoadLevelAsync(GlobalDefines.SCENE_ITEMSETTING);
    }
    else if (numString[1].Contains("iOS"))
    {
        int _nationalNum = 0;
        switch (numString[0])
        {
            case "mongol":
                _nationalNum = 3;
                break;
            case "poland":
                _nationalNum = 36;
                break;
            case "thai":
                _nationalNum = 5;
                break;
        }
        PlayerPrefsManager.SetInt(GlobalDefines.PREF_NATIONALSELECT, _nationalNum);
        ObjectManager.Instance.InitNational();
        SceneLoader.Instance.LoadLevelAsync(GlobalDefines.SCENE_ARQUIZ);

    }
    else
    {
        PlayerPrefsManager.SetInt(GlobalDefines.PREF_NATIONALSELECT, int.Parse(numString[1]));
        ObjectManager.Instance.InitNational();
        SceneLoader.Instance.LoadLevelAsync(GlobalDefines.SCENE_ARQUIZ);
    }
}
```
* 자이로센서 및 가속도센서 기능을 이용한 3D 오브젝트 360 둘러보기 구현
``` c#
if (m_PlayerState == PlayerState.MOVE) {
    if(!isGyro) {
        //Check count touches
        if (Input.touchCount > 0) {
            //Touch began, save position
            if (Input.GetTouch (0).phase == TouchPhase.Began) {
                firstpoint = Input.GetTouch (0).position;
                xAngTemp = xAngle;
                yAngTemp = yAngle;
            }
            //Move finger by screen
            if (Input.GetTouch (0).phase == TouchPhase.Moved) {
                secondpoint = Input.GetTouch (0).position;
                //Mainly, about rotate camera. For example, for Screen.width rotate on 180 degree
                xAngle = xAngTemp - (secondpoint.x - firstpoint.x) * 180.0f / Screen.width;
                yAngle = yAngTemp + (secondpoint.y - firstpoint.y) * 90.0f / Screen.height;

                if (yAngle >= 90)
                    yAngle = 90;
                else if (yAngle <= -90)
                    yAngle = -90;

                //Rotate camera
                this.transform.rotation = Quaternion.Euler (yAngle, xAngle, 0.0f);
            }
        }
    } else {
        if (Input.gyro.enabled) {
            this.transform.localRotation = new Quaternion (Input.gyro.attitude.x, Input.gyro.attitude.y, -Input.gyro.attitude.z, -Input.gyro.attitude.w);
        } else {
            // This tilts the axis of the camera like shaking a head yes
            xAngle = Input.acceleration.z * -180f;
            // This tilts like a driving wheel to make it like shaking head no
            yAngle = Input.acceleration.x * 350f;
            //        zRot = 0f;
            if (xAngle >= 90)
                xAngle = 90;
            else if (xAngle <= -90)
                xAngle = -90;
            
            transform.rotation = Quaternion.Lerp (transform.rotation, Quaternion.Euler (new Vector3 (xAngle, yAngle, 0)), Time.deltaTime * 1);
        }
    }
}
```
