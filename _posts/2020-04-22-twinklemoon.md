---
title:  "인성언어동화 반짝달"
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

### 컨텐츠 개발
### 주요 기능
* AssetBundle을 통한 컨텐츠 다운로드 구현
``` c#
IEnumerator downloadFile(Action finishListener)
{
    str_downloadURL = getDownloadURL();

    UnityWebRequest www = new UnityWebRequest(str_downloadURL);
    www.downloadHandler = new DownloadHandlerBuffer();

    AsyncOperation async = www.SendWebRequest();
    while (!async.isDone)
    {
        LoadingManager.Instance.UpdateProgress(async.progress);
        yield return null;
    }

    if (!string.IsNullOrEmpty(www.error))
    {
        NetworkingManager.Instance.ShowNetworkDelayPopup();
    }

    if (!Directory.Exists(AssetManager.GetContentsAssetDirectory()))
    {
        Debug.Log("Not exist directory");
        Directory.CreateDirectory(AssetManager.GetContentsAssetDirectory());
    }

    string zipFilePath = Path.Combine(AssetManager.GetContentsAssetDirectory(), "contents.zip");

    File.WriteAllBytes(zipFilePath, www.downloadHandler.data);

    www.Dispose();
    www = null;

    ZipUtil.Unzip(zipFilePath, AssetManager.GetContentsAssetDirectory());

    File.Delete(zipFilePath);

    PlayerPrefs.SetInt(BUNDLE_VERSION, currentVersion);

    finishListener();
}
```
* 3D 모델링에 따라말하기 기능 SAL-SA 에셋을 이용해 구현
``` c#
void SetSpeak(AudioClip clip)
{
    CurrentMode = Mode.Speak;
    obj_animController.playSpeakAnim();
    obj_outputAudio.clip = clip;

    obj_audioSource.bypassEffects = true;

    obj_salsa.SetAudioClip(clip);
    obj_salsa.Play();
}
IEnumerator waitListening()
{
    obj_tempAudio.clip = Microphone.Start(str_microphone, true, 30, RECORD_FREQUENCY);
    while (Microphone.GetPosition(str_microphone) <= 0) yield return null;
    Debug.Log("Start record.");
    List<float[]> audioDataList = new List<float[]>();
    int audioDataLength = 0;
    int recordEndCounter = 0;
    int lastPosition = 0, micPosition;
    float[] audioData;
    while (true)
    {
        micPosition = Microphone.GetPosition(str_microphone);

        int diff = 0;
        if (micPosition >= lastPosition)
        {
            diff = micPosition - lastPosition;
            lastPosition = micPosition;
        }
        else
        {
            diff = obj_tempAudio.clip.samples - lastPosition;
            lastPosition = diff;
        }
        if (diff < 0.0001f)
        {
            yield return new WaitForFixedUpdate();
            continue;
        }

        audioData = new float[diff];
        obj_tempAudio.clip.GetData(audioData, lastPosition - diff);

        float maxPeak = 0;
        for (int i = 0; i < audioData.Length; i++)
        {
            float peak = audioData[i] * audioData[i];
            if (maxPeak < peak) maxPeak = peak;
#if UNITY_IOS
            if (peak < 0.001f) audioData[i] = 0;
#else
            if (peak < 0.005f) audioData[i] = 0;
#endif

        }

        if (CurrentMode == Mode.Listen || maxPeak > f_listenSensitive)
        {
            Debug.Log("Recording..." + maxPeak);
            audioDataList.Add(audioData);
            audioDataLength += audioData.Length;
            if (CurrentMode != Mode.Listen)
            {
                SetListen();
            }
        }

        // Check recording complete
        if (CurrentMode == Mode.Listen && maxPeak < f_listenSensitive)
        {

            recordEndCounter++;
            if (recordEndCounter > CUTOFF_COUNT)
            {
                Debug.Log("ListenComplete");
                Microphone.End(str_microphone);

                float[] wholeAudio = new float[audioDataLength];
                int pointer = 0;

                int dataLength = audioDataList.Count - CUTOFF_COUNT;
                if (audioDataList.Count > CUTOFF_COUNT)
                {
                    for (int i = 0; i < audioDataList.Count; i++)
                    {
                        audioDataList[i].CopyTo(wholeAudio, pointer);
                        pointer += audioDataList[i].Length;
                    }
                    SetWait();
                    while (b_isShifting) yield return null;

                    Debug.Log("Make audio clip " + dataLength);
                    AudioClip clip = AudioClip.Create(string.Empty, pointer, 1, RECORD_FREQUENCY, false);
                    clip.SetData(wholeAudio, 0);

                    SetSpeak(clip);
                    yield break;
                }

                Debug.Log("Not enough length");
                audioDataList.Clear();
                audioDataLength = 0;
                recordEndCounter = 0;
                SetIdle();
            }
        }
        else { recordEndCounter = 0; }
        yield return new WaitForFixedUpdate();
    }
}
```
* Scratch 에셋을 이용한 색칠하기 컨텐츠 구현
``` c#
private void OnClickBrush() {
    if (enum_brush.Equals(obj_activity.GetBrushState()))
    {
        if (b_OnClick.Equals(false))
        {
            setBlockBrush(true);
            ContentsViewManager.Instance.PlaySoundEffect(SOUND_HOLD, OnBrush);
        } else {
            OffBrush();
        }
    } else {
        setBlockBrush(true);
        WrongBrush();
    }
}
void OnBrush() {
    ContentsViewManager.Instance.PlaySoundEffect(clip_objectName, () => { setBlockBrush(false); });

    if(b_InduceBrush) {
        b_InduceBrush = false;

        obj_activity.StartScratchGuide();
    }

    obj_activity.SetCurrentBrush(this);
    obj_activity.PlayScratch();

    StopInduceBrush();

    b_OnClick = true;
    gbj_on.SetActive(true);
    gbj_off.SetActive(false);
}
```
