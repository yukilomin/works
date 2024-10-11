# 連携項目/実装サンプル/確認テスト

## OSSライセンスを表示する
ライブラリを使用したら著作権表示をする
- OSSライセンス表示のための公式ライブラリ  
https://qiita.com/kurramkurram/items/4b32b9f03aa7fcbc7bf5

- 動作確認時
build.gradleのbuildTypesに isDebuggable=trueを指定しているとデバッグ用の表示になるので、  
正しい表示を確認する場合はisDebuggable=falseとする

## APIでJSON返却の確認
API接続の共通部分ができているか等を確認するためのURL  
https://yukilomin.github.io/works/testdata.json


## PDF表示の確認
以下のURLをWebViewで読み込んで確認  
https://yukilomin.github.io/works/pdfSample.pdf

## WebViewの動作を確認
以下のURLをWebViewで読み込んで確認  
https://yukilomin.github.io/works/webviewtest.html


## ランチャーアイコンのサイズ（SVGの場合）

- 背景用と前面用の２枚を用意する
- 背景用の画像の上に前面用の画像が重なるイメージになる

### 背景用画像
- 108×108で作成、SVGで出力する
- 単色の場合は画像は無しで色コードでもOK
### 前面用画像
- 108×108で作成、SVGで出力する
- 中心66×66内に画像を収め、背景は透過にする

### モノクローム対応をする場合
- 前面用の画像のみ用意する
- 背景色はシステムで自動で設定される

#### 前面用画像
- 108×108で作成、SVGで出力する
- 中心66×66内に画像を収め、背景は透過にする
- 単一色で作成する

※ 比率が守られていれば、サイズが大きい場合は自動調整されるため問題なし

### 参考
https://developer.android.com/develop/ui/views/launch/icon_design_adaptive?_gl=1*1le2th[…]*_ga_6HH9YJMN9M*MTcxNTY2NTk0Ny4xLjAuMTcxNTY2NTk0Ny4wLjAuMA
https://designpedia.jp/articles/51793

## 通知アイコンのサイズ（SVGの場合）

- SVGで出力する
- 128*128px以上（大きい場合は自動調整されるため問題なし） 
- 白と透過で作成する
- アイコンの色コードを指定する
- まるく切り取られて表示されることを考慮する（画像が大きいと表示されない領域にかかっている場合があるので注意）

### 参考
https://note.com/saawaaa/n/ned268a22567b
