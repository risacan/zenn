---
title: "初めてのクリエイティブコーディングでスイカを描いてみた"
emoji: "🍉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["クリエイティブコーディング"]
published: true
---

**動機**

以前からクリエイティブコーディングに興味があり、  [chobishiba](https://note.com/chobishibaq3) さんの作品をみて素敵だなあとため息をついていました。

クリエイティブコーディングとはプログラミングでイラストを作成したり、はたまた映像、音楽までも作ってしまう活動のことを指します。

かっこいいですよね！アート大好きな私にはもってこいな感じがします。

しかしながら以前クリエイティブコーディングに挑戦したときには、「芸術作品を作るぞ」と意気込んでしまい壮大な完成図を思い描いてスタートした結果何も形にできずに挫折するという事がありました。

今回は [虹のアーチの作り方\-Rubyでクリエイティブコーデイング\-｜chobishiba](https://note.com/chobishiba/n/nfc6cab6b55a7) こちらの記事を参考にしながら、 **なんとなく虹のアーチに形が似ているスイカを作るのを目標** に、Rubyでのクリエイティブコーディングに挑戦したいと思います。

Rubyでやりたかったのは、私がRubyistだからです ❤️

**まずは虹を作ってみる**

[p5.rb Editor](https://p5rb.ongaeshi.me/editor)を使うと、ブラウザ上でp5.rbのコーディングができます。

まずは chobishiba さんの「虹のアーチの作り方」、を参考に虹を作っていきます。ここでつまずいたら泣いてしまいますが、丁寧な記述のおかげで私でもできました！！

写経しながら進めていると、お、これはスイカに使えそうだなという箇所を見つけました。
以下のコードでは縁がありその内部に色がついた半円が描画されます。

```
〜省略〜
  stroke(0)
  fill('#eeb3b2')  # ピンクでぬりつぶした半円を描く
  arc(width/2, height/2, 180, 180, 180, 360)

  fill('#d5e8c5')  # 緑でぬりつぶした半円を描く
  arc(width/2, height/2, 155, 155, 180, 360)

  fill('#bca7c7')  # 紫でぬりつぶした半円を描く
  arc(width/2, height/2, 135, 135, 180, 360)

  fill('#eeb3b2')  # ピンクでぬりつぶした半円を描く
  arc(width/2, height/2, 120, 120, 180, 360)

  fill('#d5e8c5')  # 緑でぬりつぶした半円を描く
  arc(width/2, height/2, 110, 110, 180, 360)
〜省略〜
```

![カラフルな縁のついた緑色の半円](/images/2024adventcalendar/advent1.png)

サンプルコードでは虹を書くためにこのコードの次に「背景と同じ色の白で塗りつぶされた半円」をおいて弧を描いていますが、スイカなら半円をそのまま使えそうです！

その後無事に虹を描くことに成功しました！

![コードで描いた虹](/images/2024adventcalendar/advent2.png)

完成形のコードは chobishiba さんの [虹のアーチの作り方\-Rubyでクリエイティブコーデイング\-｜chobishiba](https://note.com/chobishiba/n/nfc6cab6b55a7) をご覧ください！


**スイカに挑戦!**

ではスイカを描いていきましょう。
写経中に気づいたことを使ってとりあえず半円の色を変えてみます

```
def setup
  createCanvas(500, 500)
  angleMode(DEGREES)
  background(255)
  noLoop
end

def draw
  stroke(0)
  fill('#04a777')
  arc(width/2, height/2, 180, 180, 180, 360)

  fill('#ffffff')
  arc(width/2, height/2, 155, 155, 180, 360)

  fill('#e71d36')
  arc(width/2, height/2, 135, 135, 180, 360)
end
```

![緑、白の縁で赤でぬりつぶされた半円](/images/2024adventcalendar/advent3.png)

お、これはもう見る人がみれば、スイカです。ただし、縁がついていますし、逆さまです。

まずは縁を消してみました。

```
def setup
  createCanvas(500, 500)
  angleMode(DEGREES)
  background(255)
  noLoop
end

def draw
  noStroke()
  fill('#04a777')
  arc(width/2, height/2, 180, 180, 180, 360)

  fill('#ffffff')
  arc(width/2, height/2, 155, 155, 180, 360)

  fill('#e71d36')
  arc(width/2, height/2, 135, 135, 180, 360)
end
```


![緑、白の縁で赤でぬりつぶされた半円、黒い縁が消えた](/images/2024adventcalendar/advent6.png)

元の虹よりもスイカっぽくなったと思います！



色々試してみたのですが、画像をそのまま逆にする方法はなさそうでした。（もしあれば教えて下さい）


世の中に半円の形をしたスイカで果肉が下に置いてあるものはないので、どうしても「いつもスイカ」の形にしたい！

とりあえず、丸を使ってスイカを半分に切って上から見たようなものを作ってみました。

`ellipse` という楕円を描くメソッドを見つけたので利用します。円を書くメソッドもあったのですが、`arc` を置き換えただけでできたのでとりあえず実装を優先します。

```
def setup
  createCanvas(500, 500)
  angleMode(DEGREES)
  background(255)
  noLoop
end

def draw
  noStroke()
  fill('#04a777')
  ellipse(width/2, height/2, 180, 180, 180, 0)

  fill('#ffffff')
  ellipse(width/2, height/2, 155, 155, 180, 0)

  fill('#e71d36')
  ellipse(width/2, height/2, 135, 135, 180, 0)
end
```

![緑、白の縁で赤でぬりつぶされた円](/images/2024adventcalendar/advent4.png)

種なしスイカを上から見たような絵になりました！
そして私はあることを思い出しました。そう、いらない部分は背景と同じ色で消してしまえばいいですね！

```
def setup
  createCanvas(500, 500)
  angleMode(DEGREES)
  background(255)
  noLoop
end

def draw
  noStroke()
  fill('#04a777')
  ellipse(width/2, height/2, 180, 180, 180, 0)

  fill('#ffffff')
  ellipse(width/2, height/2, 155, 155, 180, 0)

  fill('#e71d36')
  ellipse(width/2, height/2, 135, 135, 180, 0)

  fill("#ffffff")
  arc(width/2, height/2, 180, 180, 180, 0)
end
```

![緑、白の縁で赤でぬりつぶされた半円](/images/2024adventcalendar/advent5.png)

カットされた種なしスイカができました！ 🍉

次は種の追加です、が、なんと時間切れ！ 😭

**感想**

今回、初めて自分で考えた形を実装することができました！
chobishibaさんの作品が大好きですが、なかなか自分ではできそうにないな、と思っていましたが、まずはサンプルを見て似たような形から作ってみる、という方法なら、私でも楽しめそうです！

引き続き種の実装を進めていきます 🍉
CEASEFIRE 🍉
