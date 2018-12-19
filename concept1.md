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
