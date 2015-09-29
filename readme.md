##Superblock for iOS9
2chまとめサイトなどの広告をすべて消せます。  
[Superblockをダウンロード](https://itunes.apple.com/us/app/superblock-guang-gaoburokku/id1041786553?l=ja&ls=1&mt=8)  

##使い方
**インストールするだけでは効きません。**  
まずSafariを終了させます  
(ホームボタンを2回すばやく押し、Safariを上にドラッグして消すと終了します)  
  
次に設定アプリから  
「Safari」  
↓  
「コンテンツブロッカー」  
↓  
「Superblock」をオン  

にしてください  
  
これで広告が除去されます
これ以外に設定する必要はありません  

  
##カスタマイズ
自分用のブロックルールを作る場合はこれ以降を読み進めてください。  
  

##ドメイン指定
読み込みをブロックしたいURLのドメインを指定します  
サブドメインは自動的に全てブロックします  
例）  
zzz.aaa.jpやxxx.aaa.jpをブロックしたい場合、aaa.jpだけを指定します  
  
入力する際はドメインごとに改行してください  
  
以下のドメイン指定リストもご利用ください  
1.[Weblock List for Japanese [iOS] [日本語] [domains]](http://cosmonote.blogspot.jp/2014/02/weblock-list-for-japanese-ios-domains.html?m=1)  
2.[アダルト広告ブロック](http://sugokunemui.github.io/superblock/domain.html)  
  

##カスタムフィルターを使う  
現在インポート機能などが無いため、カスタムフィルターを使う場合は注意が必要です  
すでに入力されているカスタムフィルターを全て消して空の状態にしてから、使いたいカスタムフィルターをペーストしてください  
すでに入っているものの下に追記していくとエラーが出る可能性があります  
  
以下のいずれかをお使いください（ページを開けない場合はSuperblockをオフに）  
1.[アダルト広告＆ポップアップブロック軽量版](http://sugokunemui.github.io/superblock/adult.min.html)  
2.[アダルト広告＆ポップアップブロック](http://sugokunemui.github.io/superblock/adult.html)  
3.[相互RSSブロック](http://sugokunemui.github.io/superblock/rss.html)  
4.[アダルト広告＆ポップアップ＆相互RSSブロック](http://sugokunemui.github.io/superblock/adult.rss.html)  
  
今後複数のフィルターをインポートし統合する機能を追加します  


##カスタムフィルターを作る
この機能は上級者向けです  
WebkitのContent Blockersの仕様に従って記述します  
[Introduction to WebKit Content Blockers](https://www.webkit.org/blog/3476/content-blockers-first-look/)  
  
JSONの検証用  
[JSONLint](http://jsonlint.com/)  
  

##Content Blockersの仕様について  
コンテンツブロッカーではフィルターをJSONで表します
このJSONはトップレベルに一つの配列があり、その中に複数のフィルターオブジェクトが入ります  
  
    [フィルター1, フィルター2, ...]  
  
よく使うのはURLブロックと要素非表示です  
  

##URLをブロックする  
URLをブロックする基本的なフィルターは  
```javascript
{  
	"action":{  
		"type":"block"
	},
	"trigger":{  
		"url-filter":"example\\.com"
	}
}
```
です  
`url-filter`は常に正規表現でマッチします  
`.`はメタ文字ですのでエスケープして`\\.`にします  
(通常の正規表現ではバックスラッシュは一つですがJSONで文字列として表現する場合バックスラッシュ自体のエスケープが必要なため２つ使います)  

###単純な例
`"url-filter":"example\\.com"`
は以下のようなURLをブロックします  
```
http://example.com/homuhomu
http://sub.example.com/
https://homuhomu-example.com.jp/homuhomu
http://homuhomu.com/homu?url=http://example.com
```
example.comが含まれていれば、それがどこに出現しようとブロックします  

###厳密な例
もう少し厳密にURLをブロックするにはurl-filterの指定を以下のようにします  
`"url-filter":"//(.+\\.)?example\\.com/"`  
この条件は以下のようなURLにマッチします
```
http://example.com/
http://example.com/homuhomu
https://example.com/homuhomu
http://sub.example.com/homuhomu
https://sub.sub.example.com/homuhomu
```
example.com及びその全てのサブドメインをブロックできます  
  
###jsファイルの読み込みをブロックする 
ads.jsをブロックしたい場合は以下のようにします  
`"url-filter":"/ads\\.js"`  
  
似たようなファイル名をまとめてブロックする場合は、  
`"url-filter":"/ad.*\\.js"`  
のようにすると以下のようなファイルをすべてブロックします
```
ad.js
ads.js
advertise.js
ad_popup.js
ad_loader.js
add.js
add_count.js
adventure.js
```
このような指定の方法は目的のファイル以外もブロックしてしまうため副作用が発生する可能性があります  
  
  
###ファイル形式を指定してブロック  
ファイル形式を指定したフィルターは以下のようになります
```javascript
{  
	"action":{  
		"type":"block"
	},
	"trigger":{  
		"url-filter":"example\\.com",
        "resource-type": ["script"]
	}
}
```
`resource-type`を指定することで、画像だけブロックしたりjsファイルだけブロックすることができます  
`resource-type`には以下を指定できます  
* document  
* image  
* style-sheet  
* script  
* font  
* raw  
* svg-document  
* media  
* popup  
  
注意すべき点は`resource-type`が受け取るのは配列であるということです  
  
画像とjsファイルだけブロックする場合は以下のようにします  
`"resource-type": ["script", "image"]`  
  
すべての形式をブロックしたい場合はそもそも`resource-type`を指定すべきではありません  
  
  
###ドメインブラックリスト  
特定のドメインの場合だけ処理をするには以下の様なフィルターを使います  
```javascript
{  
	"action":{  
		"type":"block"
	},
	"trigger":{  
		"url-filter":"/ads\\.js",
        "if-domain": ["homuhomu.jp", "affiliate.com"]
	}
}
```
`if-domain`に、対象とするドメイン文字列を入れた配列を渡します  
`if-domain`に指定されたドメイン以外ではこのフィルターは無効となります  

###ドメインホワイトリスト
特定のドメインだけ処理を行わない、ホワイトリストを作るには以下のようにします
 ```javascript
{  
	"action":{  
		"type":"block"
	},
	"trigger":{  
		"url-filter":"/ads\\.js",
        "unless-domain": ["white.jp", "verygood.com"]
	}
}
```
`unless-domain`に指定されたドメインではこのフィルターが無効になります。  
それ以外のドメインでは有効になります  
  

##要素を非表示にする
CSSセレクタを使い要素の非表示ができます  
Level4の新しいセレクタも使えるそうです  
[CSS Selectors Level 4](https://drafts.csswg.org/selectors-4/#overview)  
  

###基本
`<div class="advertise"></div>`  
を非表示にするフィルターは  
 ```javascript
{
	"action": {
		"type": "css-display-none",
		"selector": ".advertise"
	},
	"trigger": {
		"url-filter": ".*"
	}
}
```
のようになります   
また、上記の指定は  
`<div class="popup advertise float show"></div>`  
のように複数のクラスを持っていてもマッチします  

`<div id="advertise"></div>`  
を非表示にする場合は  

`"selector": "#advertise"`  

のようにします  


###クラス名に特定の文字を含む場合  
`<div class="advertise"></div>`  
`<div class="ad"></div>`  
`<div class="ads"></div>` 

を非表示にするには  

`"selector": "div[class*='ad']"`  

のようにします  

`class*='substr'`はクラス名に`substr`を含んでいるものにマッチします  
  
その他マッチング例  

####前方一致
`"selector": "div[class^='substr']"`  

####後方一致
`"selector": "div[class$='substr']"`  
  
  
###特定のリンクを非表示
`<a href="http://affiliate.com/article/123456789.html">【悲報】した結果ｗｗｗ(※画像あり)</a>`  
のようなリンクを消す場合、 
  
`"selector": "a[href*='affiliate.com']"`   
  
とすれば消えます
  
  
###特定の属性を持つ要素を非表示
`<img src="homu.png" width="100" height="100">`  
のようにwidthやheightの属性を持つ要素に対し、  
  
`"selector": "img[width='100']"`  
  
とすれば、widthが100のimgを全て非表示にします  