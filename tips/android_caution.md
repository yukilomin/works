# 実装/動作確認時の注意

## 端末の戻る機能

iOSには無いため動作確認忘れがち・・

### 確認内容
端末の機能で戻る操作
- ジェスチャーナビゲーションの場合: 横にスワイプ
- 3ボタンナビゲーションの場合: ◀︎ ボタンタップ
  
  <img src="https://yukilomin.github.io/works/tips/image/gesture_navgation.png" width="200">

### 実装サンプル  
基本的には１つ前の画面に戻る  
戻る画面を変更したい場合のみ設定を入れる

```
@Compose
fun Hoge() {
    BackHandler {
        //端末の戻る機能を使用した場合の処理
    }
}
```

### 参考
https://www.android.com/intl/ja_jp/articles/51/


## 通知の許可

Android13以降はデフォルトは通知OFFになった  

### 確認内容
Android13以降でアプリ起動時に通知ONにするダイアログは出す必要があるか？  
ある場合は動作しているか？
- Android13以降: デフォルトは通知OFF
- Android13未満: デフォルトで通知ON

### 通知をONにするダイアログを入れる実装サンプル

- AndroidManifest.xml
```
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
```
- Activity
```

@Compose
fun Hoge() {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
        NotificationPermission()
    }
}

@RequiresApi(Build.VERSION_CODES.TIRAMISU)
@Composable
fun NotificationPermission() {
    val context = LocalContext.current
    val permission = Manifest.permission.POST_NOTIFICATIONS
    val requestPermissionLauncher =
        rememberLauncherForActivityResult(ActivityResultContracts.RequestPermission()) {}

    LaunchedEffect(Unit) {
        if (ContextCompat.checkSelfPermission(
                context, permission
            ) != PackageManager.PERMISSION_GRANTED
        ) {
            requestPermissionLauncher.launch(permission)
        }
    }
}
```

### 参考
https://developer.android.com/develop/ui/views/notifications/notification-permission?hl=ja&_gl=1*1f7ek9v*_up*MQ..*_ga*ODg2MDkwMDAyLjE3Mjc0OTMwNDg.*_ga_6HH9YJMN9M*MTcyNzQ5MzA0Ny4xLjAuMTcyNzQ5MzA0Ny4wLjAuMTQ1ODgxNjI3NA..


## スプラッシュスクリーン
Android12以降からアプリ起動時にデフォルトでスプラッシュスクリーンが表示されるようになった  

### 確認内容
起動時のスプラッシュスクリーンはAndroid13以降の動作に統一する必要があるか？
- Android12未満: デフォルトで表示されないため独自に実装している
- Android13以降: デフォルトで表示される

※ Android12未満を対象に実装しているアプリをAndroid13以降で確認すると、デフォルトと独自実装の2つのスプラッシュスクリーンが表示されるので注意

### スプラッシュスクリーンの表示を合わせる場合の実装サンプル
- ライブラリ

```
androidx.core:core-splashscreen:${version}"
```

- themes.xml
 
 ```
 <style name="AppTheme.Splash" parent="Theme.SplashScreen">
        <!-- 背景色. 単色 -->
        <item name="windowSplashScreenBackground">@color/splash_background</item>
        <!-- アイコン -->
        <item name="windowSplashScreenAnimatedIcon">@drawable/ic_splash</item>
        <!-- プラッシュスクリーンを閉じた後に適用するstyle -->
        <item name="postSplashScreenTheme">@style/Theme.AppTheme
        </item>
    </style>
 ```

 - AndroidManifest.xml

 ```
 <activity
    android:theme="@style/AppTheme.Splash"
 ```

 - Activity

 ```
 override fun onCreate(savedInstanceState: Bundle?) {
    installSplashScreen() // superの前に追加
    super.onCreate(savedInstanceState)
 }
 ```

### 背景色のみ表示されてiconが表示されない場合
https://issuetracker.google.com/issues/205021357?pli=1#comment14  
  - 開発中、Android StudioからのRunで起動するとアイコンは表示されない場合がある
  - startActivity(intent)で起動する場合はデフォルトではアイコンを表示しせず、背景のみ表示となる。  
  表示する場合は以下の対応を入れる。
 
      - themes.xml(Android13以降)
      ```
      <style name="AppTheme.Splash" parent="Theme.SplashScreen">
        <item name="windowSplashScreenBackground">@color/splash_background</item>
        <item name="windowSplashScreenAnimatedIcon">@drawable/ic_splash</item>
        <item name="postSplashScreenTheme">@style/Theme.AppTheme
        </item>
        <!-- (Android13以降)アイコンを表示するかどうかの制御追加 -->
        <item name="android:windowSplashScreenBehavior" tools:targetApi="33">icon_preferred</item>
    </style>

      ```
      - Activity(Android13未満)
      ```
      val intent = Intent(context, AppActivity::class.java)
      val options = Bundle()
      options.putInt("android.activity.splashScreenStyle", 1);
      context.startActivity(intent, options)
      ```

### 参考

https://developer.android.com/develop/ui/views/launch/splash-screen?hl=ja  
https://developer.android.com/develop/ui/views/launch/splash-screen/migrate?hl=ja#keep_the_custom_activity_for_branding


## テーマ別アプリアイコン
Android 13 以降で ホーム画面のアイコンをモノクロで表示できるようになった 

### 確認内容
Android13以降で端末でテーマを設定してホーム画面を表示したときに、テーマの色のアイコンを表示させる必要があるか？  
ある場合は表示されているか？

  <img src="https://yukilomin.github.io/works/tips/image/theme_color.png" width="200">


### 実装
  実装しない場合は、端末にテーマを設定しても通常のアイコンが表示される
- lancher iconを設定しているxml
```
<adaptive-icon xmlns:android="http://schemas.android.com/apk/res/android">
    <background android:drawable="@drawable/ic_launcher_background" />
    <foreground android:drawable="@drawable/ic_launcher_foreground" />

    <!-- モノクロアイコン追加 -->
    <monochrome android:drawable="@drawable/ic_launcher_monochrome" />
</adaptive-icon>
```

### 参考
https://developer.android.com/about/versions/13/features?hl=ja#themed-app-icons
https://developer.android.com/develop/ui/views/launch/icon_design_adaptive?hl=ja


## Edge−to−edge
Android15からアプリはデフォルトでEdge−to−edgeになる

### 確認内容
以下の端末で、ステータスバーとナビゲーションバーの表示を確認する
- Android15以降: デフォルトでEdge−to−edgeになる
- Android15未満: デフォルトでは対応していない

### 実装

- デフォルト

  <img src="https://yukilomin.github.io/works/tips/image/edge_to_edge_default.png" width="200">

```
    override fun onCreate(savedInstanceState: Bundle?) {
        enableEdgeToEdge() // Android15未満もedgeToEdgeに対応させる
        super.onCreate(savedInstanceState)
        setContent {
            TestTheme {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    Greeting(
                        modifier = Modifier.padding(innerPadding)
                    )
                }
            }
        }
    }

```

- isNavigationBarContrastEnforcedの設定追加

  <img src="https://yukilomin.github.io/works/tips/image/edge_to_edge_after.png" width="200">

```
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            window.isNavigationBarContrastEnforced = false // 追加
            TestTheme {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    Greeting(
                        modifier = Modifier.padding(innerPadding)
                    )
                }
            }
        }
    }

```

- コンテンツをステータスバーに被らせたい場合  

  <img src="https://yukilomin.github.io/works/tips/image/edge_to_edge_overlap.png" width="200">

ScaffoldのinnerPaddingがinnerPaddingがシステムバーにコンテンツを被らないようにしているため、  
Scaffoldを使用しない、またはinnerPaddingを調整する

```
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            window.isNavigationBarContrastEnforced = false
            TestTheme {
                Scaffold(
                    modifier = Modifier.fillMaxSize(),
                    contentWindowInsets = WindowInsets(0.dp) // 追加
                ) { innerPadding ->
                    Greeting(
                        modifier = Modifier.padding(innerPadding)
                    )
                }
            }
        }
    }
```

- カメラの領域にも表示を被らせたくない場合  
ScaffoldにdisplayCutoutPaddingを付与する

```
Scaffold(
    modifier = Modifier.fillMaxSize().displayCutoutPadding()
    ...
```

### 参考
https://developer.android.com/develop/ui/views/layout/edge-to-edge?hl=ja


## ダイナミックカラー
ユーザーが端末に設定したカラー（ダイナミックカラー）をアプリに反映させることが可能

### 確認内容
Android12以降では端末にテーマを設定した場合としていない場合とで確認する
- Android12以降: ダイナミックカラーの設定可能
- Android12未満： ダークモードとライトモードのみ

  <img src="https://yukilomin.github.io/works/tips/image/theme_color.png" width="200">

※ ダイナミックカラーを使用しないアプリの場合も、自動でダイナミックカラーが適用されていないか確認が必要

### 実装

- Theme.kt にデフォルトで設定されている

```
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

```

- 同じテーマのままダークモード/ライトモードの色にする場合  
（例）青のライトモードのテーマを設定しているが、青のダークモードのテーマで画面を表示したい場合

```
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            dynamicLightColorScheme(context) // darkThemeを条件に入れずに指定
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

```

- システムバーのテキストカラーの変更方法

```
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            val window = (view.context as Activity).window
            WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars =
                isLightColor(colorScheme.background.toArgb())
            WindowCompat.getInsetsController(window, view).isAppearanceLightNavigationBars =
                isLightColor(colorScheme.background.toArgb())
        }
    }
```
### 参考
https://www.android.com/intl/ja_jp/articles/322/#section-1

