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