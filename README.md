## 　　Lesson9.　ブラウザからURLを取得する 
#### 開発メモ
### 1.ページ翻訳のコア
　google翻訳を利用します
<br>  最終的なURLは下記なので、U=の部分に翻訳したいURLを入れればよさそうですね
```
https://translate.google.com/translate?sl=en&tl=ja&u=　
```
### 2.選択中の文字列を取得する
　HOTKEYワークフローの設定で実装しています
<br>　HOTKEYの設定タブのArgumentでSelection in macOSを選択します
### 3.クリップボードの内容を取得する（特定の変数をスクリプトに渡す）
　Alfredワークフローで利用できる変数の{clipboard:0}を利用します
<br>　数字は0が直近のクリップボード、1が1つ前、2が2つ前というように履歴参照ができます
<br>
<br>　シェルスクリプトで利用できるようにArg and Valsユーティリティを利用します
<br>　Variables:に下記の項目を追加します。
<br>　Name:clipboard Value:{clipboard:0}　
<br>　こうすると、後続のワークフローで＄clipboardとして利用できます
<br>
<br>　※選択アイテムがない場合にクリップボードを取得するロジックを作っていますが、
<br>　　実際には、直近のクリップボードにURLかどうか覚えていないので実用的ではないです
<br>　　まあ、クリップボードの練習ですね
### 4.正しいURLかどうか確認する
　AlfredのConditionalユーティリティを利用します
<br>　このユーティリティは正規表現を条件に指定できるので、URLのチェックに使ってみました
<br>　ネットから下記の正規表現を拾ってきましたが、ちんぷんかんぷんですっ！
<br>　https?://[\w!\?/\+\-_~=;\.,\*&@#\$%\(\)'\[\]]+ 
<br>
<br>　あとConditionalユーティリティでちょっと躓きました
<br>　thenとelseの後にテキストボックスがあるので、後続に受け渡すパラメータを描くのかと思ったりしましたが
<br>　違いました。ワークフローをわかりやすくするためのテキストです
<br>　今回は、URLパターンにマッチしたら『翻訳』、アンマッチなら『エラー』としています
<br>　ワークフローの緑色のオブジェクトを開いてみてくださいね
### 5.翻訳させる
　Open URLを使ってページ翻訳をしています。英語→日本語固定です
<br>  直前のスクリプトで対象ページのURLをechoして{query}に受け渡しています
<br>　ちなみにですが、日本語ページを英語ページに変えるほうが面白いですね
### 6.エラー処理をつける　
　HOTKEYスタートなので、エラーの際のフィードバックとしてLarge Typeをつかってみました
### 7.追加機能：
　・HOTKEY『⌥s』を押すとsafariで開いているページを翻訳
<br>　・HOTKEY『⌥c』を押すとchromeで開いているページを翻訳
<br>（ホットキーはご自身での設定が必要です）
### 8.ブラウザで表示されているURLを取得する
 AppleScriptを利用します。例のアプリケーションにTellする独特な言語です
<br> osascriptコマンドを経由させるとシェルスクリプトで利用できます
<br> 以下の構文です
```
 osascript -e 'AppleScript'
```
<br> さて具体的に、Safariで表示しているURLを取得するには以下でOK
``` 
 osascript -e 'tell app "safari" to get the url of the current tab of window 1'
```
<br> もうひとつChromeの場合はこうです
```
 osascript -e 'tell app "google chrome" to get the url of the active tab of window 1'
```
<br> そしてこれらのosascriptコマンドの結果をOpen URLに受け渡すためバックスラッシュ(`)で囲ってエコーしています
<br> 多層ネストスクリプト！　これは世界がひろがりますね　　
<br>
<br> ※Alfred4がアプリケーションへアクセスすることを許可する必要があります
<br>  システム環境設定→セキュリティとプライバシー→プライバシータブ→オートメーション
<br>  Alfred4にsafariやgoogle chromeを制御する許可を付与
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

