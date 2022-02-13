#### 開発メモ
ワークフロー
<BR><img width="600" src="https://user-images.githubusercontent.com/40127279/126857141-8dfc15db-5f1c-41a6-8c2a-5abdaa204d69.png">

### 1.ページ翻訳のコア
　google翻訳を利用します
<br>　最終的なURLは下記なので、U=の部分に翻訳したいURLを入れればよさそうですね
```
https://translate.google.com/translate?sl=en&tl=ja&u=　
```
### 2.選択中の文字列を取得する
　HOTKEYワークフローの設定で実装できます
<br>　具体的には、HOTKEYの設定タブのArgumentでSelection in macOSを選択すればOK
<br>　 
<br>　Hotkey
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126857171-fb42d3dc-1d58-4d29-a26d-724924e20082.png">
### 3.クリップボードの内容を取得する（特定の変数をスクリプトに渡す）
　Alfredワークフローで利用できる変数の{clipboard:0}を利用します
<br>　数字は0が直近のクリップボード、1が1つ前、2が2つ前というように履歴参照ができます
<br>
<br>　シェルスクリプトで利用させるためにArg and Valsユーティリティを利用します
<br>　Variables:に下記の項目を追加します。
<br>　Name:clipboard　Value:{clipboard:0}　
<br>　こうすると、後続のワークフローで＄clipboardという変数として扱えます
<br>
<br>　※選択アイテムがない場合にクリップボードを取得するロジックを作っていますが、
<br>　　実際には、直近のクリップボードがURLかどうかユーザーは覚えていないので実用的ではないです
<br>　　まあ、クリップボード利用の練習と思ってください
<br>　
<br>　Arg and Vals
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126857201-0d78df0e-c32c-4c4f-b9fb-93177066192e.png">
<br>　
<br>　RunScript
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126857365-aaffe8c7-e13e-44a2-8c75-7af912131378.png"> 
### 4.正しいURLかどうか確認する
　AlfredのConditionalユーティリティを利用します
<br>　このユーティリティは正規表現を条件に指定できるので、URLのチェックに使ってみました
<br>　ネットから下記の正規表現を拾ってきましたが、ちんぷんかんぷんですっ！
<br>　`https?://[\w!\?/\+\-_~=;\.,\*&@#\$%\(\)'\[\]]+ `
<br>
<br>　あとConditionalユーティリティでちょっと躓きました
<br>　thenとelseの後にテキストボックスがあるので、後続に受け渡すパラメータを書くのかと
<br>　思ったりしましたが、違いました。ワークフローをわかりやすくするためのテキストです
<br>　今回は、URLパターンにマッチしたら『翻訳』、アンマッチなら『エラー』としています
<br>　ワークフローの緑色のオブジェクトを開いてみてくださいね
<br>　
<br>　Conditional
<br>　<img width="600"  src="https://user-images.githubusercontent.com/40127279/126857221-eb5ac214-1812-431d-8ab0-feac3cec0202.png">

### 5.翻訳させる
　Open URLを使ってページ翻訳をしています。翻訳は英語→日本語固定です
<br>  直前のスクリプトで対象ページのURLをechoして{query}に受け渡しています
<br>　ちなみにですが、日本語ページを英語ページに変えるほうが見ていて面白いですね
<br>　
<br>　OpenURL
<br>　<img width="600"  src="https://user-images.githubusercontent.com/40127279/126857239-7362c7b1-52f9-4c3f-a80f-5d9c54594ff5.png">
 
### 6.エラー処理をつける　
　HOTKEYスタートなので、エラーの際のフィードバックとしてLargeTypeを使ってみました
<br>　
<br>　LargeType
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126857315-f24dc73e-65e7-40fe-a60d-926ab5068d7b.png">

### 7.追加機能：
　・HOTKEY『⌥s』を押すとsafariで開いているページを翻訳
<br>　・HOTKEY『⌥c』を押すとchromeで開いているページを翻訳
<br>　（ホットキーはご自身での設定が必要です）
### 8.ブラウザで表示されているURLを取得する：
　上記の機能の実装はAppleScriptを利用しています
<br>　AppleScriptというのは例のアプリケーションにTellする独特な言語です
<br>　osascriptコマンドで、AppleScriptが利用できるようになります
<br>　以下の構文です
```
 osascript -e 'AppleScript'
```
<br> さて具体的に、Safariで表示しているURLを取得するには以下
``` 
 osascript -e 'tell app "safari" to get the url of the current tab of window 1'
```
<br> もうひとつChromeの場合はこうです
```
 osascript -e 'tell app "google chrome" to get the url of the active tab of window 1'
```
<br>　そしてこれらのosascriptコマンドの結果をOpen URLに受け渡すためバックスラッシュ(`)で囲って
<br>　エコーしています。多層ネストスクリプトです！これは世界がひろがりますね　　
<br>　
<br>　※Alfred4がアプリケーションへアクセスすることを許可する必要があります
<br>　　システム環境設定→セキュリティとプライバシー→プライバシータブ→オートメーション
<br>　　Alfred4にsafariやgoogle chromeを制御する許可を付与
<br>　
<br>　RunScript
<br>　<img width="600"  src="https://user-images.githubusercontent.com/40127279/126857408-ca5fa338-5dd3-4438-8999-d82e9b3de478.png">
<br>　 
#### 取扱説明
### 機能:
　選択中もしくはクリップボードのURLのサイトを日本語に翻訳する
<br>　※ワークフローのArg and Vals,Conditionalユーティリティを利用するサンプルです
### インストール:
　1.[alfredworkflow](https://github.com/KitanoTamotsu/translate/releases/download/1.1/Translate.Webpage.by.google.alfredworkflow.zip)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録
### 使い方:
　翻訳したいのページのURLを選択（もしくはブラウザで表示させて）してHOTKEYで起動
（ホットキーはご自身で設定が必要です）
#### 修正履歴
### ver1.1(2021-04-04)：
　シェルスクリプトをbashからzshに変更
<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)

