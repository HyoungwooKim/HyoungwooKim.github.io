---
title:  "REDPEN 만점 습관"
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

### 메인 개발
### 주요 기능
* Timeline 기능으로 애니메이션 구현
* 서버 통신을 통해 데이터 연동
``` c#
IEnumerator Co_OnLoadChildList()
{
    WWWForm form = new WWWForm();

    UnityWebRequest request = UnityWebRequest.Post(CHILD_LIST_DEV, form);
    request.SetRequestHeader("appName", GlobalDefine.GetAppName());
    request.SetRequestHeader("appVer", GlobalDefine.GetAppVerison());
    request.SetRequestHeader("authToken", GlobalDefine.GetAuthToken());
    request.SetRequestHeader("deviceSN", GlobalDefine.GetDeviceSN());

    yield return request.SendWebRequest();

    if (request.isNetworkError || request.isHttpError)
    {
        Debug.Log(request.error);
    }
    else
    {
        JsonData _data = JsonMapper.ToObject(request.downloadHandler.text);
        int _listCount = _data["resultData"].Count;

        if (_listCount > 0)
        {
            for (int i = 0; i < _data["resultData"].Count; ++i)
            {
                Child _child = new Child();
                _child.childData = (string)_data["resultData"][i]["childData"];
                _child.userId = (string)_data["resultData"][i]["userId"];
                _child.userNm = (string)_data["resultData"][i]["userNm"];
                if (_data["resultData"][i]["classNum"] != null)
                {
                    _child.classNum = (int)_data["resultData"][i]["classNum"];
                } else
                {
                    _child.classNum = 0;
                }
                _child.schoolyearNo = (int)_data["resultData"][i]["schoolyearNo"];
                _child.classJoinStatus = (string)_data["resultData"][i]["classJoinStatus"];

                list_children.Add(_child);
            }
        }
    }
}
```

