# Unity Vibration Implementation for Android

## 📱 Overview
Hướng dẫn implement vibration (rung) trong Unity cho Android với error handling và permission management. Có thể áp dụng cho bất kỳ Unity project nào.

## 🔧 Setup Requirements

### 1. Android Manifest Permission
Tạo file `Assets/Plugins/Android/AndroidManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.unity3d.player"
    android:versionCode="1"
    android:versionName="1.0">

    <uses-sdk android:minSdkVersion="22" android:targetSdkVersion="33" />

    <!-- Vibration Permission - REQUIRED -->
    <uses-permission android:name="android.permission.VIBRATE" />
    
    <!-- Optional Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />

    <application
        android:theme="@style/UnityThemeSelector"
        android:hardwareAccelerated="true"
        android:resizeableActivity="false"
        android:screenOrientation="portrait"
        android:configChanges="orientation|keyboardHidden|screenSize"
        android:label="@string/app_name"
        android:icon="@mipmap/app_icon">

        <activity
            android:name="com.unity3d.player.UnityPlayerActivity"
            android:label="@string/app_name"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:hardwareAccelerated="true"
            android:screenOrientation="portrait"
            android:exported="true">
            
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            
            <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
        </activity>

        <meta-data android:name="unity.splash-mode" android:value="0" />
        <meta-data android:name="unity.build-id" android:value="1.0.0" />
        
    </application>

</manifest>
```

## 💻 Code Implementation

### 2. VibrationManager.cs - Core Vibration System

```csharp
using UnityEngine;

public class VibrationManager : MonoBehaviour
{
    private static VibrationManager instance;
    public static VibrationManager Instance
    {
        get
        {
            if (instance == null)
            {
                instance = FindObjectOfType<VibrationManager>();
                if (instance == null)
                {
                    GameObject go = new GameObject("VibrationManager");
                    instance = go.AddComponent<VibrationManager>();
                    DontDestroyOnLoad(go);
                }
            }
            return instance;
        }
    }

    [Header("Settings")]
    [SerializeField] private bool isVibrationEnabled = true;
    
    private void Awake()
    {
        if (instance == null)
        {
            instance = this;
            DontDestroyOnLoad(gameObject);
            LoadVibrationSettings();
        }
        else if (instance != this)
        {
            Destroy(gameObject);
        }
    }

    #region Vibration Methods

    /// <summary>
    /// Trigger vibration with default duration (100ms)
    /// </summary>
    public void Vibrate()
    {
        if (!isVibrationEnabled) return;
        
        #if UNITY_ANDROID && !UNITY_EDITOR
            try
            {
                using (AndroidJavaClass unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
                {
                    AndroidJavaObject currentActivity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
                    if (currentActivity != null)
                    {
                        AndroidJavaObject vibrator = currentActivity.Call<AndroidJavaObject>("getSystemService", "vibrator");
                        if (vibrator != null)
                        {
                            // Check if device has vibrator
                            bool hasVibrator = vibrator.Call<bool>("hasVibrator");
                            if (hasVibrator)
                            {
                                vibrator.Call("vibrate", 100L); // 100ms vibration
                                Debug.Log("[VIBRATION] Vibration triggered successfully");
                            }
                            else
                            {
                                Debug.LogWarning("[VIBRATION] Device does not have vibrator");
                            }
                        }
                        else
                        {
                            Debug.LogError("[VIBRATION] Failed to get vibrator service");
                        }
                    }
                    else
                    {
                        Debug.LogError("[VIBRATION] Failed to get current activity");
                    }
                }
            }
            catch (System.Exception e)
            {
                Debug.LogError($"[VIBRATION] Error during vibration: {e.Message}");
            }
        #elif UNITY_IOS && !UNITY_EDITOR
            // iOS vibration
            Handheld.Vibrate();
        #else
            // Editor fallback
            Debug.Log("[VIBRATION] Vibration would trigger here (Editor mode)");
        #endif
    }

    /// <summary>
    /// Trigger vibration with custom duration
    /// </summary>
    public void Vibrate(long milliseconds)
    {
        if (!isVibrationEnabled) return;
        
        #if UNITY_ANDROID && !UNITY_EDITOR
            try
            {
                using (AndroidJavaClass unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
                {
                    AndroidJavaObject currentActivity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
                    if (currentActivity != null)
                    {
                        AndroidJavaObject vibrator = currentActivity.Call<AndroidJavaObject>("getSystemService", "vibrator");
                        if (vibrator != null)
                        {
                            // Check if device has vibrator
                            bool hasVibrator = vibrator.Call<bool>("hasVibrator");
                            if (hasVibrator)
                            {
                                vibrator.Call("vibrate", milliseconds);
                                Debug.Log($"[VIBRATION] Vibration triggered for {milliseconds}ms");
                            }
                            else
                            {
                                Debug.LogWarning("[VIBRATION] Device does not have vibrator");
                            }
                        }
                        else
                        {
                            Debug.LogError("[VIBRATION] Failed to get vibrator service");
                        }
                    }
                    else
                    {
                        Debug.LogError("[VIBRATION] Failed to get current activity");
                    }
                }
            }
            catch (System.Exception e)
            {
                Debug.LogError($"[VIBRATION] Error during vibration: {e.Message}");
            }
        #elif UNITY_IOS && !UNITY_EDITOR
            // iOS vibration
            Handheld.Vibrate();
        #else
            // Editor fallback
            Debug.Log($"[VIBRATION] Vibration would trigger for {milliseconds}ms (Editor mode)");
        #endif
    }

    /// <summary>
    /// Check if device has vibration capability
    /// </summary>
    public bool HasVibrator()
    {
        #if UNITY_ANDROID && !UNITY_EDITOR
            try
            {
                using (AndroidJavaClass unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
                {
                    AndroidJavaObject currentActivity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
                    if (currentActivity != null)
                    {
                        AndroidJavaObject vibrator = currentActivity.Call<AndroidJavaObject>("getSystemService", "vibrator");
                        if (vibrator != null)
                        {
                            return vibrator.Call<bool>("hasVibrator");
                        }
                    }
                }
            }
            catch (System.Exception e)
            {
                Debug.LogError($"[VIBRATION] Error checking vibrator: {e.Message}");
            }
        #endif
        return false;
    }

    #endregion

    #region Settings Management

    /// <summary>
    /// Toggle vibration on/off
    /// </summary>
    public void ToggleVibration()
    {
        isVibrationEnabled = !isVibrationEnabled;
        PlayerPrefs.SetInt("VibrationEnabled", isVibrationEnabled ? 1 : 0);
        PlayerPrefs.Save();
        Debug.Log($"[VIBRATION] Vibration {(isVibrationEnabled ? "enabled" : "disabled")}");
    }

    /// <summary>
    /// Check if vibration is enabled
    /// </summary>
    public bool IsVibrationEnabled()
    {
        return isVibrationEnabled;
    }

    /// <summary>
    /// Load vibration settings from PlayerPrefs
    /// </summary>
    private void LoadVibrationSettings()
    {
        isVibrationEnabled = PlayerPrefs.GetInt("VibrationEnabled", 1) == 1;
    }

    #endregion
}
```

### 3. VibrationHelper.cs - Static Helper Methods

```csharp
using UnityEngine;

public static class VibrationHelper
{
    /// <summary>
    /// Trigger vibration with default duration
    /// </summary>
    public static void Vibrate()
    {
        if (VibrationManager.Instance != null)
        {
            VibrationManager.Instance.Vibrate();
        }
        else
        {
            Debug.LogWarning("[VIBRATION] VibrationManager not found!");
        }
    }

    /// <summary>
    /// Trigger vibration with custom duration
    /// </summary>
    public static void Vibrate(long milliseconds)
    {
        if (VibrationManager.Instance != null)
        {
            VibrationManager.Instance.Vibrate(milliseconds);
        }
        else
        {
            Debug.LogWarning("[VIBRATION] VibrationManager not found!");
        }
    }

    /// <summary>
    /// Check if vibration is enabled
    /// </summary>
    public static bool IsVibrationEnabled()
    {
        if (VibrationManager.Instance != null)
        {
            return VibrationManager.Instance.IsVibrationEnabled();
        }
        return false;
    }

    /// <summary>
    /// Toggle vibration on/off
    /// </summary>
    public static void ToggleVibration()
    {
        if (VibrationManager.Instance != null)
        {
            VibrationManager.Instance.ToggleVibration();
        }
    }

    /// <summary>
    /// Check if device has vibration capability
    /// </summary>
    public static bool HasVibrator()
    {
        if (VibrationManager.Instance != null)
        {
            return VibrationManager.Instance.HasVibrator();
        }
        return false;
    }
}
```

### 4. Usage Examples

```csharp
// Basic vibration
VibrationHelper.Vibrate();

// Custom duration vibration
VibrationHelper.Vibrate(200L); // 200ms

// Check if vibration is enabled
if (VibrationHelper.IsVibrationEnabled())
{
    VibrationHelper.Vibrate();
}

// Toggle vibration
VibrationHelper.ToggleVibration();

// Check device capability
if (VibrationHelper.HasVibrator())
{
    VibrationHelper.Vibrate();
}
```

### 5. Game Event Integration Examples

```csharp
// In any game script
public class GameEvents : MonoBehaviour
{
    public void OnPlayerDeath()
    {
        VibrationHelper.Vibrate(300L); // Long vibration for death
    }

    public void OnItemCollect()
    {
        VibrationHelper.Vibrate(50L); // Short vibration for collection
    }

    public void OnButtonPress()
    {
        if (VibrationHelper.IsVibrationEnabled())
        {
            VibrationHelper.Vibrate(25L); // Very short for UI feedback
        }
    }

    public void OnComboAchievement()
    {
        VibrationHelper.Vibrate(500L); // Long vibration for achievements
    }
}
```

## 🔍 Key Features

### ✅ Error Handling
- Try-catch blocks để handle Android API errors
- Null checks cho currentActivity và vibrator service
- Device capability check với `hasVibrator()`

### ✅ Platform Support
- Android: Native Android vibration API
- iOS: Unity's Handheld.Vibrate()
- Editor: Debug logs for testing

### ✅ Debug Logging
- Tagged logs với `[VIBRATION]` prefix
- Detailed error messages
- Success/failure status logging

### ✅ Settings Integration
- Vibration can be toggled on/off
- Settings saved in PlayerPrefs
- Singleton pattern for easy access

## 🚨 Common Issues & Solutions

### Issue 1: Vibration not working on Android
**Solution:**
- Check AndroidManifest.xml has `VIBRATE` permission
- Verify device has vibration capability
- Check logcat for error messages

### Issue 2: Vibration works in editor but not on device
**Solution:**
- Ensure `#if UNITY_ANDROID && !UNITY_EDITOR` condition
- Test on real device (emulators may not support vibration)
- Check device vibration settings

### Issue 3: Vibration too weak/strong
**Solution:**
- Adjust duration: `Vibrate(50L)` for shorter, `Vibrate(500L)` for longer
- Consider device-specific vibration patterns
- Test on multiple devices

## 📋 Testing Checklist

- [ ] AndroidManifest.xml has VIBRATE permission
- [ ] VibrationManager has proper error handling
- [ ] Vibration settings can be toggled
- [ ] Works on real Android device
- [ ] Debug logs show proper status
- [ ] No crashes when vibration is disabled
- [ ] Performance impact is minimal

## 🎮 Integration Examples

### Game Events that trigger vibration:
- Player death/respawn
- Item collection
- Button presses
- Achievement unlocks
- Level completion
- Combat hits
- Power-up activation

### Vibration Patterns:
```csharp
// UI feedback
VibrationHelper.Vibrate(25L);

// Item collection
VibrationHelper.Vibrate(50L);

// Combat hit
VibrationHelper.Vibrate(100L);

// Achievement
VibrationHelper.Vibrate(300L);

// Death/Game Over
VibrationHelper.Vibrate(500L);
```

## 📱 Device Testing

### Test on these devices:
- Samsung Galaxy series
- Google Pixel series
- OnePlus devices
- Xiaomi devices
- Huawei devices

### Test scenarios:
- Vibration enabled/disabled
- Different vibration durations
- Multiple rapid vibrations
- Battery saver mode
- Silent mode

## 🔧 Build Settings

### Player Settings:
- Target API Level: 22+ (Android 5.0+)
- Scripting Backend: IL2CPP
- Target Architectures: ARM64

### Build Configuration:
- Development Build: Enable for debugging
- Script Debugging: Enable for detailed logs
- Compression Method: LZ4HC for smaller builds

## 🎯 Setup Instructions

### 1. Create VibrationManager
- Tạo empty GameObject trong scene
- Add VibrationManager component
- Đặt tên "VibrationManager"

### 2. Add to Scene
- VibrationManager sẽ tự động DontDestroyOnLoad
- Có thể access từ bất kỳ script nào qua VibrationHelper

### 3. Test Implementation
```csharp
// Test trong Start() method
void Start()
{
    // Test vibration
    VibrationHelper.Vibrate();
    
    // Test custom duration
    VibrationHelper.Vibrate(200L);
}
```

---

## 📝 Notes

- Always test on real devices, not emulators
- Consider user preferences for vibration
- Respect device battery and accessibility settings
- Use vibration sparingly to avoid annoyance
- Log all vibration events for debugging

## 🔗 Related Files

- `Assets/Scripts/VibrationManager.cs`
- `Assets/Scripts/VibrationHelper.cs`
- `Assets/Plugins/Android/AndroidManifest.xml`

---

*Last updated: [Current Date]*
*Unity Version: 2022.3 LTS*
*Target Platform: Android*
