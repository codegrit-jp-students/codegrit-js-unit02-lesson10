# レッスン10. 開発のベストプラクティス

## 目的

- 読みやすいコードを書くべき理由を知る。
- プログラミングのベストプラクティスを知る。
- ESLintを導入する。

## 読みやすいコードを書こう

プログラムを書いていくと、段々と全体のコード量が増えるにつれて全体の複雑性がどんどんと上がってきます。気をつけて設計を行わないと、一つのプログラムを変更すれば全体に影響を及ぼすことになり、結果的にどこにも手を付けられない状態、あるいは著しく生産性が低い状態に陥ってしまいます。複雑性が増大してしまうことは避けられないことではありますが、いくつかのルールを守ることでそのペースを落とすことは出来ます。

## 一つのことを上手くやる

ソフトウェア開発の有名な規範で[「UNIX哲学」](https://ja.wikipedia.org/wiki/UNIX%E5%93%B2%E5%AD%A6)というものがあります。この中で、「一つのことだけを上手くやるプログラムを書くこと（"Do one thing, and do it well"）」という言葉が格言としてあります。

Javascriptで開発を行う場合も、極論を言えば、index.jsファイル一つに必要なコードを全てまとめて入れることは出来ます。しかし、それでは最初は良くても次第に全体像が見えなくなります。読みやすいコードを書くために、役割ごとにファイルを分割して書くようにしましょう。

例えば、Lesson6で作成したValidatorのクラスは、`lib`というフォルダの中に配置しました。また、幾つかのValidatorクラスを一つのファイルにまとめるのではなく、`baseValidator.js`、`mailValidator.js`、`passwordValidator.js`という3つのファイルを作成しました。このように、似た役割のクラスであっても異なるファイルに分け役割をより明確にすることが推奨されます。`lib`の中に更に`validators`のようなディレクトリを作りファイルを配置するのも良いアイデアです。

## 適切な命名を行う

プログラマは変数やファンクションの名前から、その変数に何を入れるのかや、ファンクションの目的を判断します。そのため、意図が判断できないような名前を付けるべきではありません。

```javascript
/* 悪い例 */
let n;
function sTest() {}

/* 良い例 */
let name;
function soundTest() {}
```

また、変数名を短縮してかける場合は出来るだけ短くしましょう。

```javascript
/* 悪い例 */
let userCollection;

/* 良い例 */
let users
```

## Don't Repeat Yourself - 繰り返しを避ける

プログラミングの世界では、"Don't Repeat Yourself"という有名な言葉があります。これは、プログラムにおいて何度も同じようなコードが出現するのを防ぐことを指します。似た動きをするファンクションなどは抽象度を高めて、一つにまとめることでより複雑性の低いコードを書くことが出来ます。

例えば、レッスン9で作成した`PasswordValidator`というクラスですが、`_cannotEmpty`という空白文字かどうかをチェックするファンクションを定義しました。しかし、パスワード以外でも、メールアドレスや名前など様々なものでも空白文字チェックが必要になってくるでしょう。そこでBaseValidatorというクラスを作成して、その中に`_cannotEmpty`メソッドは含めるようにすると便利です。

```javascript
/* 悪い例 */

class PasswordValidator {
  constructor(val) {
    this.val = val;
  }
  _cannotEmpty() {
    if (!!this.val) {
      return { success: true };
    } else {
      return { success: false, , message: "パスワードは必須です。" };
    }
  }
}

class EmailValidator {
  constructor(val) {
    this.val = val;
  }
  _cannotEmpty() {
    if (!!this.val) {
      return { success: true };
    } else {
      return { success: false, message: "メールアドレスは必須です。" };
    }
  }
}

```

上記の例ですが、_cannotEmptyファンクションの違いは、バリデーション失敗時のメッセージのみです。このメッセージですが、`${type}は必須です。`のようにテンプレートリテラルを使ってまとめてしまえます。そこで、BaseValidatorというクラスを作成します。

```javascript
/* 良い例 */

class BaseValidator {
  constructor(type, val) {
    this.type = type;
    this.val = val;
  }
  _cannotEmtpy() {
    if (!!this.val) {
      return { success: true };
    } else {
      return { success: false, message: `${this.type}は必須です。`};
    }
  }
}

class EmailValidator extends BaseValidator {
  constructor(val) {
    super('メールアドレス', val);
  }
}

class PasswordValidator extends BaseValidator {
  constructor(val) {
    super('パスワード', val);
  }
}
```

こうすることで、2度ほぼ同じような`_cannotEmpty`ファンクションを定義することを避けることが出来ました。


## ESLintでコードをチェックする

ESLintというツールを使うことで、一定のコーディング規約に沿ってコードが書かれているかを自動でチェックすることが出来ます。

### ESLintを導入する。

ESLintを導入するには、まずライブラリをインストールします。コーディング規約は自分で設定することも出来ますが、AirBnBやGoogleが公開しているコーディング規約を自動で適用することも出来ます。今回はAirBnBの規約を適用します。

```bash
$ yarn add -D eslint
```

次に`eslint --init`コマンドで.eslintrcという規約設定ファイルを作成します。

```bash
$ eslint --init
```

すると、様々な質問がされます。

```bash
? How would you like to configure ESLint?
```

"Use a popular style guide"を選びます。すると、AirBnB、Googleなどの規約から設定を選べるのでAirBnBを選んでください。

```bash
? Which style guide do you want to follow? Airbnb (https://github.com/airbnb/javascript)
```

後は、Reactを利用しているかなど質問をいくつか聞かれるので次のように答えていきます。

```bash
? Do you use React? No
? What format do you want your config file to be in? JavaScript
Checking peerDependencies of eslint-config-airbnb-base@latest
? The style guide "airbnb" requires eslint@^4.19.1. You are currently using eslint@5.0.1.
  Do you want to downgrade? Yes
Local ESLint installation not found.
The config that you've selected requires the following dependencies:

eslint-config-airbnb-base@latest eslint@^4.19.1 eslint-plugin-import@^2.12.0
? Would you like to install them now with npm? Yes
```

完了すると、.eslintrc.jsというファイルが作成されます。

`.eslintrc.js`
```javascript
module.exports = {
  "extends": "airbnb-base"
};
```

AtomやVS Codeのようなエディタを利用している場合、自動的に.eslintrc内のコーディング規約が適用されて、修正箇所が表示されるはずです。例えばindex.jsというファイルを作成して次のコードを書くと、4つのメッセージが表示されます。

```javascript
const test = "test"
```

- 'test' is assigned a value but never used. (no-unused-vars) => testという変数が定義されているがその後利用されていないことを伝えるメッセージ
- Strings must use singlequote. (quotes) => 文字列が''(シングルクォート)で囲まれてないというメッセージ
- Newline required at end of file but not found. (eol-last) => ファイルの最後に新しいラインが必要というメッセージ
- Missing semicolon. (semi) => セミコロンがないというメッセージ

いかがでしょうか。このように細かく指摘を自動で行ってくれるので、ESLintに従うだけで良いコードが書きやすくなります。AirBnBのJavaScriptコーディング規約については、以下のページより詳細を読むことが出来ます。それぞれ、なぜその規約を使うべきなのかの解説があるので勉強になるはずです。

- [AirBnB JavaScriptコーディング規約](https://github.com/airbnb/javascript)

### エディターごとの設定

ESLintはコマンドラインで動かすこともできますが、エディタのプラグインを導入するとより便利になります。

Atomエディタの場合は、"linter-eslint"というプラグインをインストールしましょう。

VSCodeを利用している場合、特にプラグインを利用せずとも自動的に.eslintrcのファイルを認識してくれます。

## 適切なコメントを残す

コードにコメントを残す場合は、以下のことに気をつけましょう。

1. コメントの意味が後から見ても、他の人が見ても分かるようにする。
2. コードを見れば自明なことは、コメントを書かない。

1については、例えば「後で修正する」のようなコメントがあっても、何を修正するのか何のために修正するのかが分かりません。より具体的なコメントを残すことで自分が後で見たときも何を書いているかが分かりやすくなります。

2については、書いている通りなのですが、コードを見れば何をしているか分かるようなものにたいして、コメントを書いても視認性が下がるだけで良いことをありません。

例えば、以下の例は極端ですがコードで自明なものにコメントをしている例です。

```javascript
let username; // ユーザーネームを保存する変数
```

コメントを残す際には、そのコメントが後から見ても価値を持っているかどうか、またコードから自明なことを書いていないかを意識しましょう。


## README.mdを書く

README.mdは、他のユーザーに対してそのリポジトリの内容を説明するために利用します。また、他のユーザーだけでなく自分のプロジェクトであっても時間が経てば、なんのためのリポジトリか忘れてしまう可能性がありますので、簡単にコードの利用方法や説明を書きましょう。

README.mdのmdはマークダウンという形式で書かれたファイルを意味しています。例えばよく使うマークダウンの記法は以下の通りです。

### マークダウンの記法

1. ヘディング

```markdown
# H1と同一
## H2と同一
### H3と同一
```

2. リスト

```markdown
1. ナンバー1
2. ナンバー2
3. ナンバー3
```

```markdown
- リスト1
- リスト2
- リスト3
```

3. 画像へのリンク

```markdown
![画像名](画像URL)
```

4. ハイパーリンク

```markdown
[リンク先](リンク先URL)
```

5. インラインコード

```markdown
`function() {}`
```

6. コード

"```javascript"でコードを開始しして"```"でコードを閉じます。

7. 強調

```markdown
*強調したい部分*を囲む
```

### README.mdに含める内容

READMEに含める内容は以下を目安にしましょう。

#### 1. プロジェクト名称
#### 2. プロジェクトの概要
#### 3. インストール方法 

例えばyarnを使う場合ならyarnコマンドを最初に実行する必要があるので、それについて書きましょう。

#### 4. 使い方

CodeGritのコースではwebpackを使ってビルドを行っていますが、プロジェクトによってビルド方法は様々ですので、どのコマンドを打つと良いのかやnpmスクリプトの説明を入れる必要があります。

プロジェクトによっては、これに加えてコードのライセンスや、コントリビューター(プロジェクトに貢献した人)のリストなどが書かれていることも多いです。

まずは上記の1-4をREADME.mdに含めるようにしていきましょう。

## 一貫性を保つ

コーディング規約とも言われますが、コードの書き方を揃えることでコードの可読性を不必要に下げることがないようにすることが推奨されます。例えば上述のBaseValidatorクラスを見てみましょう。

```javascript
/* 悪い例 */

class BaseValidator {
  constructor( type, val ) { // 引数を与える()内の左右に無駄なスペースがある
    this.type = type; // 他の部分ではインデントはスペース4つだがここは2つになっている。
    this.val = val;
  }
  _cannotEmtpy() {
    if (!!this.val){ // 引数を与える()とその後のブロック{}の間にスペースがない。
      return {success: true}; // オブジェクトの{}内の左右にスペースがない。
    } else {
      return { success: false, message: `${this.type}は必須です。` };
    }
  }
}

/* 良い例 */

class BaseValidator {
  constructor(type, val) {
    this.type = type;
    this.val = val;
  }
  _cannotEmtpy() {
    if (!!this.val) {
      return {success: true};
    } else {
      return { success: false, message: `${this.type}は必須です。` };
    }
  }
}

```

上記の悪い例は極端ですが、実際には引数の()とその後のブロック開始{の間にスペースがあったりなかったりするケースやインデントが揃ってないケースというのは、よく見かけます。こうした一貫性についてはESLintでもチェック出来ますが、チェックをする前から常に一貫性のあるコードを書くように心がけましょう。(上述のESLintを使うことでスペースの数などについては一貫性を保って書くことが簡単に出来ます。)

## コードを分けて視認性を上げる。

1行あたりの文字数が多くなりすぎたり、一行で複数のことを行っているとコードの視認性が悪くなります。例えば、Promiseのコードを考えてみます。

```javascript
return promiseA.then(promiseB).catch(doSomething());
```

このように一行にまとめてコードを書くことも出来ますが、次のようにした方が何をしているかが分かりやすくなります。

```javascript
return promiseA
  .then(promiseB)
  .catch(doSomething());
```

また、オブジェクトやJSONを書く場合も一行に全てをまとめるのではなく、3つ以上キーがある場合は分割しましょう

```javascript
// 悪い例

const user = { name: 'Steve Jobs', email: 'steve@apple.com', nickname: 'steve', position: 'Founder' };

// 良い例

const user = {
  name: 'Steve Jobs', 
  email: 'steve@apple.com', 
  nickname: 'steve', 
  position: 'Founder'
}
```

## エンジニア向けサービスやコミュニティを活用する

### StackOverflowを利用する

世界的に有名なエンジニア向けのQ&Aサービスで[StackOverflow](https://stackoverflow.com/)があります。プログラムを書いていて、どうしても考えても分からないという場合には(目安としては30分以上)、このサービスで質問をすると他のエンジニアが質問に答えてくれます。考えずに投稿するのは、他のエンジニアの時間を奪ってしまうため避けましょう。StackOverflowには日本語版もありますが、日本では[teratail](https://teratail.com/)というサービスがより有名です。ただ英語の方がコミュニティが大きいため、StackOverflowの方がより良い回答が帰ってくる可能性は高いと考えれれます。そのため、英語力がある程度あるという場合にはStackOverflowを利用することをおすすめします。

### オープンソースのコードを調べる

最後に、勉強の方法に関するベストプラクティスです。これまで使ってきたNPMのパッケージですが、これらは全てオープンソースのプロジェクトとして開発されています。オープンソースとはどういうことかというと、コードが一般公開されていて、誰でもその開発に参加することが出来ることをいいます。オープンソースの中には、アプリケーションまるごと公開されているものも多くあります。例えばですが、有名なものの一つに"ToDoMVC"というプロジェクトがあります。このプロジェクトは、同じToDoリストのサービスを異なるJavascriptフレームワーク、ES5のみ、ES6のみなど、様々なバージョンで見ることが出来ます。

- [ToDoMVC](http://todomvc.com/)

このサイトのコードはよくメンテナンスされており、アプリケーションの規模も小さいので、良いコードの書き方を学習するには最適ですので、是非ご覧になって下さい。


### 勉強会に参加する

在住地域にもよりますが、エンジニアは良く勉強会を開きます。闇雲にこうした勉強会に参加しても得るものは多くはありませんが、例えば興味のある分野の勉強会に参加して、優秀なエンジニアと知り合うなどすると勉強になるだけではなくて、モチベーションアップにも繋がります。また、こうした勉強会から就職に繋がるケースもあるでしょう。例えば、[TECH PLAY](https://techplay.jp/)というサイトでは、勉強会を簡単に探すことが出来ます。


### Qiitaに学んだことを投稿する

国内の有名なエンジニア向けのサービスで[Qiita](https://qiita.com/)があります。このサービス上では様々なエンジニアが自分の得た知見について記事を書きシェアしています。記事を書くにあたって、間違いがあってはいけないため、より自分の理解が出来てから書こうというモチベーションが生まれます。そのため、記事を書く課程で理解が深まったり、新しい学びを得たり出来ます。また、記事に対して他のエンジニアからフィードバックがありますので、そこからも得るものが多いでしょう。

## チャレンジ

- [チャレンジ10](./challenge/README.md)

## 更に学ぼう

### 記事から学ぶ

- [AirBnB JavaScriptコーディング規約](https://github.com/airbnb/javascript)