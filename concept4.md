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
