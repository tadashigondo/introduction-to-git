# みんなで使う - ベアリポジトリとクローン,リモートリポジトリ

さて、前回までで、ひとりで使うぶんには基本的な使い方ができるようになりましたね。では、これからはみんなで Git を使って行きましょう。ここからは学習のためにひとり何役もこなしてもらいますよ！

# まずは準備

恒例の準備から行いましょう。とりあえず、みんなで触るためのリポジトリを作ってしまいましょう。そして、今からあなたが演じるのは「たかし」です。

たかしは、今からあるプロジェクトを始めます。まずはそのためのディレクトリとリポジトリを作成しましょう。ディレクトリの名前は「takashis_workspace」にしましょうか。ではそこにリポジトリを作ってください。

できましたか？

そして、あなたはこのディレクトリ内にいるときはたかしくんです。なので、たかしくんとして振る舞うための設定をしておきましょう。

    $ cd path/to/takashis_workspace
    $ git config user.name TAKASHI
    $ git config user.email takashi@example.com

これで、このディレクトリ内ではあなたの名前は TAKASHI になりますし、メールアドレスも takashi@example.com となります。

まずは、なにかコミットしましょう。いつものやつにしますか。

* cat_lover_said.txt

```
猫は動物の一種である。

犬とならび、ペットとして飼われることの多い動物である。

猫は人間のことを下僕かなにかだと思っており、また人間も
人間で猫に使役されることを至上の喜びと思う傾向にある。

猫の魅力に取り付かれた人間は二度と猫の魅力から離れることが
できないとも言われており、猫ってほんとうにかわいいですね。

もうほんとうに可愛い、猫。
猫。
猫が本当にかわいい。

猫好きじゃない人間とか頭がおかしいとしか思えない。
```

上のような内容のテキストを作成し、コミットしてください。コミットメッセージは「猫好きの話を追加」でいいですね。

さて、準備はいいですね。では、まずは今回のブランチの運用のポリシーを決めましょう。

まず、masterブランチですが、master ブランチにコミットするものは「リリースするもの」だけ、ということにしましょう。開発途中のものをコミットするのは、「development」というブランチにします。そして、作業は development からトピックブランチを切って行います。

言い方を変えると、なにか作業をするときには development ブランチから topic ブランチを切って行います。そして、その作業が終わったら、develop ブランチでそのトピックブランチを merge します。これを繰り返して、development ブランチの状態が「リリースできるもの」になったら、master ブランチから development ブランチを merge します。

こうすることによって、

* master が指してるコミットは、必ず 「最新のリリース」 である
* development が指しているコミットは、「最新の開発版」である
* 各トピックブランチが指しているコミットは、「作業途中の状態」である

ということが保証されます。なんか便利な気がしますね。ディレクターから「今の最新版チェックしたいから見せて」と言われたら、「自分で development ブランチ見ろや」って言えますし、ユーザーから不具合報告が来たら、master ブランチの内容を精査すればいいのです。

では、このような開発を進めるために、たかしくんであるあなたは、まずは development ブランチを作っておきましょう。

    $ git branch development

はい、いいですね。

## ゆうすけがプロジェクトに参戦！

さて、ここで、このプロジェクトにあたらしくゆうすけが参戦することになりました。嬉しいですね！今回は簡単のため、たかしもゆうすけも同じマシン上で開発を行っているという設定にしましょう。ほんらいならば、おそらくネットワーク越しに協調作業を行うことになるのでしょうが、その場合もやることはほとんど変わりません。

さて、たかしであるあなたは、「ゆうすけとどうやって作業を一緒にやろうかな」と考えています。"ゆうすけのリポジトリ" と "たかしのリポジトリ" の間で変更内容をやりとりしてもいいのですが、このあともしもさらにだれかがプロジェクトに参戦した場合、三人のリポジトリで互いに変更内容をやりとりするのは想像するだけで大変ですね。

なので、ふたりが共通でさわるリポジトリを作ってしまいましょう。それぞれ、自分のリポジトリで行った変更をこの共通のリポジトリに反映させたり、相手が行った変更をこの共通のリポジトリから取ってきたりする、という運用でいきます。

では、この「みんなで触るリポジトリ」を、「たかしのリポジトリ」と同じ内容で作ってみましょう！takashis_workspace ディレクトリから出て、

    $ git clone --bare path/to/takashis_workspace path/to/shared_repo.git

と打ってみましょう。 path/to/takashis_workspace は、たかしの作業ディレクトリのあるパス、path/to/shared_repo.git は、あたらしく作る「みんなで触るリポジトリ」を作成したいパスです。

こんな表示が出てくるはずです。

```
Cloning into bare repository 'shared_repo.git'...
done.
```

「bare repository 'shared_repo.git' を作ったよ」だそうです。bare repository？ なんだそれは？

それを知るために、作られたリポジトリの正体を見てみましょう。ファイラーでもなんでもいいです。作られたリポジトリはどんなファイルですか？ そう、ディレクトリですね。そして、その中になんかいろいろごちゃごちゃとファイルが入っているのがわかるでしょう。

ここで、本書の最初で見た「リポジトリの実体」がなんだったか、思い出してみましょう。そうです、作業ディレクトリの中に作られた、「.git」というディレクトリでしたね。リポジトリは、ただのディレクトリです。そして、今回の「bare repository」もディレクトリでした。

勘のいいひとはもうわかったかもしれませんね。

実は、 

    $ git clone --bare コピー元 コピー先

とすると、コピー元から作業ディレクトリなしでリポジトリだけをコピーしてくるのです。

「みんなで触るリポジトリ」で作業を行うことはないので、このリポジトリには作業ディレクトリは要らないですよね。みんなで触るリポジトリをこういう「bare repository」で作るのは、実はもうひとつ理由があるのですが、それはまた改めて説明しましょう。

さて、これで無事に「みんなで触るリポジトリ」が出来上がりました。

では、今からあなたは「ゆうすけ」です。ゆうすけはこのプロジェクトに参戦するために、手元にこのプロジェクトのリポジトリが必要ですね。

というわけで、ゆうすけはこの「みんなで触るリポジトリ」から、自分の手元にリポジトリをクローンしましょう。

    $ git clone path/to/shared_repo.git path/to/yuusukes_workspace

はい、"--bare" なしでcloneしてみました。

では、ゆうすけの作業ディレクトリではゆうすけとして振る舞う設定をしましょう。

    $ cd path/to/yuusukes_workspace
    $ git config user.name YUUSUKE 
    $ git config user.email yuusuke@example.com

はい、いいですね。

## ゆうすけ、混乱する

さて、ここで、ゆうすけは例の「文体変更」のお仕事を始めようとしました。development ブランチからトピックブランチを切るのでしたね。ちゃんと development ブランチがクローンされてきてるか、git branch で確認しましょう。

    $ git branch
    * master
    
！？ ない！？ グラフも確認しましょう。

    $ git graph
    * 253585d  (HEAD, origin/master, origin/development, origin/HEAD, master) 2013-05-07 TAKASHI 猫好きの話を追加

なんか知らないのたくさんある！怖い！！

怖いですね。説明しましょう。

## リモートリポジトリとはなにか

さて、この状態を説明するためには、「リモートリポジトリ」というものを説明しないといけません。「リモートリポジトリ」とはなんでしょうか。その名のとおり、「遠隔地のリポジトリ」ですね。今回の共同作業で登場したリポジトリは、みっつです。

* たかしのリポジトリ
* ゆうすけのリポジトリ
* みんなで触るリポジトリ

でしたね。そして、ゆうすけは、ゆうすけのリポジトリで変更した内容を「みんなで触るリポジトリ」に反映させなければなりません。あるいは、たかしが行った内容が反映されたものを、「みんなで触るリポジトリ」から取得しなければなりません。

こういう、「あのリポジトリにコミット内容を反映させたい」とか「あのリポジトリの変更内容を取得してきたい」というようなことをするためには、「あのリポジトリ」にあたるリポジトリを、「リモートリポジトリ」として登録しておかなければなりません。

逆の言い方をすると、「リモートリポジトリ」を登録しておくと、そのリポジトリに対して、手元のリポジトリの変更を反映させたり、逆にそのリポジトリから変更を取り込んだりできるわけです。

そして、git clone でどこかのリポジトリを手元に clone してきた場合、Git さんが勝手に clone 元のリポジトリを、「origin」という名前のリモートリポジトリとして登録してくれるのです。気が利く！

では、その様子を見てみましょう。

あなたはゆうすけです。今ゆうすけの作業ディレクトリにいますね？

では、そこで、 git remote と打ってみましょう。

    $ git remote
    origin

はい、originが表示されました。これはつまり、「リモートリポジトリとしてoriginというリポジトリが登録されているよ」ということです。

ふむふむなるほど。

それでは、それを念頭においてから、先ほどの graph の結果を眺めてみましょう。

    $ git graph
    * 253585d  (HEAD, origin/master, origin/development, origin/HEAD, master) 2013-05-07 TAKASHI 猫好きの話を追加

origin/master, origin/development, origin/HEAD というみっつのブランチがありますね。これらはそれぞれ、「origin リモートリポジトリのmasterブランチ」「origin リモートリポジトリの development ブランチ」「origin リモートリポジトリのHEAD」を表しています。git clone をすると、クローン元に存在するコミット手元のリポジトリにコピーされ、クローン元に存在するブランチを、「リモートリポジトリのブランチ」という形で手元のリポジトリにコピーしてきます。これらのブランチは「リモートブランチ」と呼ばれます。

これらのリモートブランチは、「自分の物」ではないので、このリモートブランチを選択して作業をすることはできません。作業をするときには、このリモートブランチを「追跡」するための手元のブランチを切って、そのブランチ上で作業を行います。

今は、development ブランチが手元に必要ですね。では、「origin/development」を「追跡」する、手元の development ブランチを作成しましょう。リモートブランチを追跡するブランチを作成するためには、 「git branch 手元のブランチの名前 追跡したいリモートブランチの名前」です。やってみましょう。

    $ git branch development origin/development
    Branch development set up to track remote branch development from origin.

「development ブランチは、 origin から来た development リモートブランチを追跡するように設定されたよ」と言われましたね。いい感じです。

これで、ゆうすけは手元の development ブランチで行った反映を origin の development ブランチに反映したり、その逆に origin の development ブランチで行われた変更を自分の development ブランチに適用することができるようになりました。

ところで、さきほど git clone してきたときに、development ブランチは手元にありませんでしたが、 master ブランチはすでに手元に存在していましたね？これはどういうことでしょうか。

git clone を行ったとき、まずはクローン元のリポジトリに存在するコミットとブランチがコピーされるのは上で見た通りです。じつは、Git さんはそのあと、origin/master ブランチを追跡する master ブランチを勝手に作成してくれて、さらに作業ディレクトリ内にこの master ブランチを checkout するところまで自動でやってくれているのです。

なるほどねー。

## 一方たかしは？

では、たかしのほうはどうでしょうか。今度はたかしになって、たかしの作業ディレクトリに移動してみてください。

```
$ cd path/to/takashis_workspace
$ git remote
```

なにも表示されませんね。

これは当然で、たかしのリポジトリは、clone で作ったリポジトリではないので、Git さんが勝手にリモートリポジトリを登録してくれたりはしていないのです。じゃあ、たかしのほうからも「みんなで触るリポジトリ」に対して変更を反映できたりそこから変更を取得してきたりできるように、remote リポジトリを設定しておきましょう。

```
$ git remote add origin path/to/shared_repo.git
```

さて、これでリモートリポジトリが設定されました。確認してみましょう。

```
$ git remote
origin
```

うん、よさそうですね。では、graph も確認しましょう。

```
$ git graph
* a10bcb5  (HEAD, master, development) 2013-05-07 Shinpei Maruyama 猫好きの話を追加
```

アレッ！？ origin/* ブランチがないですね！？

それもそのはず、今は「リモートリポジトリはここだよ」と設定はしましたが、そのリモートリポジトリに存在しているコミットやブランチを手元のリポジトリに持ってきていません。ゆうすけの場合、git clone を行うことでリモートリポジトリのコミットやブランチが手元にコピーされてきましたが、たかしはリモートリポジトリをクローンしたわけではないですからね。

では、リモートリポジトリに存在するコミットやブランチを手元に持ってきましょう。そのために使うコマンドが、 git fetch です。"git fetch ＜リモートリポジトリの名前＞" で、リモートリポジトリの中身を手元に持って来れます。やってみましょう。

```
$ git fetch origin
From path/to/shared_repo
 * [new branch]      development -> origin/development
 * [new branch]      master     -> origin/master
```

path/to/shared_repo の部分には origin のリポジトリのパスが表示されているでしょう。そして、ふたつの新しいブランチが生まれていますね。矢印の左側に「リモートリポジトリでのブランチの名前」、矢印の右側に「そのブランチを手元で表すためのリモートブランチ」です。

さて、これで準備は OK でしょうか？まだですね。今たかしの手元には development ブランチと master ブランチが存在していますが、このブランチがそれぞれ origin/development と origin/master を追跡するようにしないと、手元で行った変更が origin/development や origin/master に反映されません。なので、次は手元の master, development がそれぞれ origin/master, origin/development を追跡するように設定しましょう。

そのためには、"git branch --set-upstream-to=＜追跡したいリモートブランチ＞ ＜手元のブランチ＞" というコマンドを利用します。

ではやってみましょう。

    $ git branch --set-upstream-to=origin/master master
    Branch master set up to track remote branch master from origin.
    
    $ git branch --set-upstream-to=origin/development development
    Branch development set up to track remote branch development from origin.

うん、いい感じに設定できましたね。

ようやくこれでみんなで Git を使う準備が整いました！今回はリポジトリを用意するところから初めて、しかも二人分の環境をひとりで設定したのでなんだか大変でしたが、実際のプロジェクトでは多くの場合「ゆうすけ」のポジションで開発を行うことになるでしょうから、そんなに大変なことはありません。「みんなで触るリポジトリ」を git clone してきて、必要に応じて手元で追跡ブランチを作成すればいいだけですね。

では、次回から、実際に共同作業を行ってみましょう。