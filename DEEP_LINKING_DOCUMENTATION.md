# Deep Linking Implementation - Dino Dodge!

## 📋 Overview

This document explains the deep linking system implemented for **Dino Dodge!** Android game. Deep linking allows users to open your game directly from web links, making it easy to share and promote your game.

---

## 🎯 What is Deep Linking?

**Deep linking** is a way to open your mobile app using a special URL (web link). When someone clicks a link, the app opens automatically if it's installed. If not, they're redirected to download it from the Play Store.

### Real-World Example:
- User sees a link on a website or social media
- They click it → Your game opens instantly
- If they don't have the game → They go to Play Store to download

---

## 🛠️ What We Implemented

### 1. **Android Manifest Configuration**
**File:** `Assets/Plugins/Android/AndroidManifest.xml`

We configured the Android app to respond to specific URLs:

```xml
dinododge://open
dinododge://open?scene=intro
```

**Key Components:**
- **Scheme:** `dinododge` - Your app's unique identifier
- **Host:** `open` - The action to perform
- **Parameters:** `?scene=intro` - Optional scene to load

**Technical Details:**
- Added `android:launchMode="singleTask"` - Prevents multiple app instances
- Added `android:autoVerify="true"` - Enables App Links for better integration
- Configured intent filters to catch deep link URLs

---

### 2. **Deep Link Manager Script**
**File:** `Assets/deep.cs`

This Unity C# script handles what happens when someone opens the app via a deep link.

**Features:**
- ✅ Automatically detects when app is opened via deep link
- ✅ Loads the correct scene based on URL parameter
- ✅ Falls back to default scene if no parameter is provided
- ✅ Extracts additional parameters from URLs
- ✅ Validates scene names before loading

**How It Works:**

```
1. User clicks link: dinododge://open?scene=intro
2. Android opens Dino Dodge app
3. DeepLinkManager detects the URL
4. Script extracts "intro" from the scene parameter
5. Checks if "intro" is in the valid scenes list
6. Loads the "intro" scene
```

**Configuration in Unity:**
- Attach `DeepLinkManager` script to a GameObject in your first scene
- Set `linkName` to `"open"`
- Add valid scene names to `validScenes` list (e.g., "intro", "menu", "game")

---

### 3. **Web Redirect Page**
**File:** `index.html` (Hosted on GitHub Pages)
**URL:** https://jeevanjiji.github.io/redirect/

Since custom URL schemes (`dinododge://`) don't work as clickable links on websites, we created a web page that handles the redirect.

**How It Works:**

```
1. User clicks the GitHub Pages link
2. Web page attempts to open: dinododge://open
3. If app installed → Opens the game
4. If app NOT installed → Redirects to Play Store after 1.5 seconds
```

**Features:**
- Single "Play Now" button
- Automatic fallback to Play Store
- Clean, mobile-friendly design
- Works on all web platforms (itch.io, social media, etc.)

---

## 📱 Supported URL Formats

| URL | What It Does |
|-----|-------------|
| `dinododge://open` | Opens app, loads first scene in validScenes |
| `dinododge://open?scene=intro` | Opens app, loads "intro" scene |
| `dinododge://open?scene=menu` | Opens app, loads "menu" scene |
| `https://jeevanjiji.github.io/redirect/` | Smart link: Opens app OR redirects to Play Store |

---

## 🔧 Implementation Steps (Summary)

### Step 1: Android Manifest Setup
- Created custom AndroidManifest.xml
- Added deep link intent filters
- Configured `dinododge://open` scheme

### Step 2: Unity Script Integration
- Created DeepLinkManager.cs script
- Added scene validation system
- Implemented URL parameter parsing
- Set up DontDestroyOnLoad for persistence

### Step 3: Bug Fixes
- Removed `BaseUnityGameActivityTheme` activity (caused build errors)
- Disabled custom keystore (removed password prompts)
- Added default scene fallback for URLs without parameters

### Step 4: Web Redirect Page
- Created HTML redirect page
- Hosted on GitHub Pages
- Implemented smart app detection
- Added Play Store fallback

---

## 🧪 How to Test

### Method 1: ADB Command (PC Required)
```bash
adb shell am start -W -a android.intent.action.VIEW -d "dinododge://open?scene=intro"
```

### Method 2: HTML File on Phone
Create `test.html` on your Android device:
```html
<!DOCTYPE html>
<html>
<body>
<a href="dinododge://open">Open Dino Dodge!</a>
</body>
</html>
```

### Method 3: Web Browser
Open: https://jeevanjiji.github.io/redirect/

### Method 4: Send Link via WhatsApp/SMS
Send yourself: `dinododge://open?scene=intro`
(Note: May not be clickable, but can be copied and opened in browser)

---

## 📂 File Structure

```
Dino/
├── Assets/
│   ├── deep.cs                          # Deep link handler script
│   └── Plugins/
│       └── Android/
│           └── AndroidManifest.xml      # Android configuration
├── index.html                            # Web redirect page
└── DEEP_LINKING_DOCUMENTATION.md         # This file
```

---

## ⚙️ Configuration Reference

### DeepLinkManager Component (Inspector)

| Property | Description | Example |
|----------|-------------|---------|
| **Link Name** | URL identifier | `"open"` |
| **Valid Scenes** | Scenes that can be loaded | `["intro", "menu", "game"]` |

### AndroidManifest.xml Settings

| Setting | Value | Purpose |
|---------|-------|---------|
| **Scheme** | `dinododge` | Your app's unique URL scheme |
| **Host** | `open` | Base path for deep links |
| **Launch Mode** | `singleTask` | Prevents multiple app instances |
| **Auto Verify** | `true` | Enables Android App Links |

---

## 🚀 Usage Examples

### For Itch.io Game Page
```
Add this link to your game description:
https://jeevanjiji.github.io/redirect/

Text: "Play on Android" or "Launch Game"
```

### For Social Media Marketing
```
Share: https://jeevanjiji.github.io/redirect/
Caption: "🦖 Play Dino Dodge now! Click to launch the game instantly!"
```

### For In-Game Sharing
```
Share text: "Join me in Dino Dodge! dinododge://open"
Fallback link: https://jeevanjiji.github.io/redirect/
```

### For Email Campaigns
```
Button text: "Play Now"
Link: https://jeevanjiji.github.io/redirect/
```

---

## 🐛 Troubleshooting

### Issue: App doesn't open when clicking link
**Solution:** 
- Make sure app is installed on the device
- Check if the URL scheme matches: `dinododge://open`
- Verify DeepLinkManager script is in the initial scene

### Issue: Wrong scene loads
**Solution:**
- Check scene name spelling in URL matches Unity scene name
- Verify scene is added to validScenes list in Inspector
- Ensure scene is in Build Settings

### Issue: Build fails with keystore error
**Solution:** 
- Already fixed: Custom keystore removed from ProjectSettings
- If issue persists: Edit > Project Settings > Android > Publishing Settings > Uncheck "Custom Keystore"

### Issue: Build fails with BaseUnityGameActivityTheme error
**Solution:**
- Already fixed: GameActivity removed from AndroidManifest
- Only UnityPlayerActivity is used

---

## 📊 Technical Architecture

```
┌─────────────────────────────────────────────┐
│         User clicks web link                │
│   https://jeevanjiji.github.io/redirect/    │
└─────────────────┬───────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────┐
│         Web page JavaScript                 │
│   Attempts: dinododge://open?scene=intro    │
└─────────────────┬───────────────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
┌──────────────┐    ┌──────────────────┐
│  App Found   │    │  App Not Found   │
│  Android     │    │  Redirect to     │
│  Opens App   │    │  Play Store      │
└──────┬───────┘    └──────────────────┘
       │
       ▼
┌─────────────────────────────────────────────┐
│         Unity App Launches                  │
│    Application.deepLinkActivated fires      │
└─────────────────┬───────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────┐
│       DeepLinkManager.OnDeepLinkActivated   │
│   - Parses URL                              │
│   - Extracts scene parameter                │
│   - Validates scene name                    │
│   - Loads scene or defaults to first valid  │
└─────────────────────────────────────────────┘
```

---

## 🎓 Explaining to Your Team

### Simple Explanation:
"We added a feature that lets people open our game by clicking a web link. It's like a shortcut that opens the app instantly. If they don't have the app, it takes them to the Play Store to download it."

### Technical Explanation:
"We implemented Android deep linking using a custom URL scheme (`dinododge://`). The AndroidManifest registers our app as a handler for these URLs. When opened, Unity's DeepLinkManager script processes the URL and loads the appropriate scene. For web compatibility, we created a GitHub Pages redirect that handles both installed and non-installed states."

### Marketing Explanation:
"Users can now click a single link to instantly start playing our game. This makes it super easy to share on social media, embed in websites, or use in ads. One click and they're playing!"

---

## 📝 Future Enhancements

### Potential Improvements:
1. **Analytics Integration:** Track which links drive the most installs
2. **Dynamic Parameters:** Pass user IDs, referral codes, or level data
3. **Universal Links:** Add HTTPS domain linking (requires domain ownership)
4. **iOS Support:** Implement similar system for iOS using Universal Links
5. **QR Codes:** Generate QR codes that open the app
6. **Campaign Tracking:** Add UTM parameters for marketing analytics

---

## 📞 Support & Resources

### Key Files to Backup:
- `Assets/deep.cs`
- `Assets/Plugins/Android/AndroidManifest.xml`
- `index.html` (or GitHub repo)

### Unity Documentation:
- [Application.deepLinkActivated](https://docs.unity3d.com/ScriptReference/Application-deepLinkActivated.html)

### Android Documentation:
- [Deep Links](https://developer.android.com/training/app-links/deep-linking)
- [App Links](https://developer.android.com/training/app-links)

---

## ✅ Checklist for New Builds

Before releasing a new version:

- [ ] Verify DeepLinkManager is in the first scene
- [ ] Update validScenes list if scene names changed
- [ ] Test deep link with `adb shell` command
- [ ] Test web redirect page
- [ ] Ensure AndroidManifest.xml is not overwritten
- [ ] Test on real Android device
- [ ] Update Play Store link in index.html if package name changes

---

## 📄 Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Dec 11, 2025 | Initial deep linking implementation |
| 1.1 | Dec 11, 2025 | Fixed build errors (keystore, theme) |
| 1.2 | Dec 11, 2025 | Added web redirect page |
| 1.3 | Dec 12, 2025 | Documentation created |

---

**Document created by:** GitHub Copilot  
**Last updated:** December 12, 2025  
**Game:** Dino Dodge! by BitForge  
**Platform:** Android
