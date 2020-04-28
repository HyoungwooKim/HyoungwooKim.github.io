---
title:  "똑똑과학단추"
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
* AVPro를 통한 Video Player 구현
* Timeline 기능으로 애니메이션 구현
``` c#
public virtual void StartNextActivity(){
    i_currActivity++;
    if(i_currActivity < arr_activities.Length){
        arr_activities[i_currActivity].StartActivity(this);
    } else {
        EndContentsView();
    }
}
public void PlayPlayableAssets(PlayableAsset asset, Action listener){
    if(obj_director == null){
        Debug.Log("There is no playable director.");
    }
    if(asset != null) {
        obj_playableListener = listener;
        obj_director.Play(asset);
    }
    else if(listener != null) listener();
}
```
