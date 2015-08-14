% 型の勉強
% ka ([kaosfield](http://www.kaosfield.net))

# 注意

自分自身の復習や予習も兼ねてのスライドです

間違いや勘違いが含まれる可能性が多分にあります

# 型とは

値の集合の形

<ul>
<li>1の型はInteger</li>
<li>"a"の型はString</li>
<li>3.14の型はFloating</li>
</ul>

# 関数にも型がある

関数自体も無名関数が即時に作れて変数に代入したり出来るので値である

フォン・ノイマン型の世界においてはプログラム自体がメモリ上に格納されて変化しうる単なる値であるとも考えられる

# 関数の型

```c
int double(int x)
{
  return x * 2;
}
```

これの型は$Integer → Integer$と書く

```java
string toString(int x) {
  return toString(x);
}
```

これの型は$Integer → String$と書く

# 高階関数の型

```javascript
function f(g) {
  return g(1) * g(2)
}

function g1(x) {
  return x + 1
}

function g2(x) {
  return x * 2
}
```

g1とg2の型は
$Integer → Integer$

fの型は$Integer → Integer$を引数にして$Integer$を返すので

$(Integer → Integer) → Integer$

# 引数の数が2以上の場合

```javascript
function product(x, y) {
  return x * y
}
```

これは高階関数を使えば一引数の関数だけで実装可能になる

```javascript
function product(x) {
  return function(y) {
    return x * y
  }
}

product(2)(3) //=> 6
```

これを関数のカリー化と言う

# カリー化された関数の型

```javascript
function product(x) {
  return function(y) {
    return x * y
  }
}
```

まず外側のfunctionに注目

$Integer$ である x を受け取り関数を返している

$Integer → (T → S)$という型になる($T$, $S$は型変数)

返している関数の型は$Integer$ yを受け取り$Integer$を返り値にしているので$Integer → Integer$である

以上より関数productの型は$Integer → (Integer → Integer)$である

# $→$の結合の優先順位

右結合にするのが一般的である

すなわち

$Integer → Integer → Integer$

は

$Integer → (Integer → Integer)$

である

同様に
$T_1 → T_2 → T_3 → T_4$
は
$T_1 → (T_2 → (T_3 → T_4))$
である

# 関数の型の読み方(1)

$T_1 → T_2 → T_3 → T_4$

型が$T_1, T_2, T_3$の3つの引数を受け取り型が$T_4$の戻り値を返す関数の型

と読める

## 例

```javascript
function(x, y, z) {
  return x + y + z
}
```

# カリー化すると

```javascript
function(x) {
  return function(y) {
    return function(z) {
      return x + y + z
    }
  }
}
```

# 関数の型の読み方(2)

$(T_1 → T_2) → (T_3 → T_4)$

型が$T1 → T2$となる関数を引数に受け取り型が$T_3→T_4$となる関数を戻り値とする関数の型

と読める

## 例

```javascript
function (f) {
  return function(x) { /* ... */ }
}
```

# 型クラス

オブジェクト指向で言う所のインタフェイスみたいなもの

Haskellでは$Integer$型は$Ord$という型クラスのインスタンスである

$Ord$型クラスに属する型に属する値はOrdinaryでなければならない(順序を持ってなければならない)

## 例

$Ord$型クラスに属する$Integer$型の値1, 2, 3...は順序を持っている

```
... < 0 < 1 < 2 < 3 < 4 < ...
```

# 型クラスの継承関係

$Ord$型クラスは$Eq$型クラスを継承している

$Eq$型クラスは属する型の値が同値かどうかが確認出来なければならない(=の演算が出来なければならない)

※代入ではない

$Integer$と$Floating$型クラスは$Num$型クラスを継承している

# Maybe型やOptional型

<ul>
<li>HaskellにはMaybe型</li>
<li>SwiftにはOptional(Int)型で実現されるInt?</li>
</ul>

などがある

# 型コンストラクタ

型クラスは型コンストラクタを持つ

型コンストラクタは型を引数に取ることがある

Array型クラスなどはIntegerやFloatingなどの型を取りIntegerやFloatingの配列型となる

C++のtemplateを知ってる人は想像すると分かりやすいかも

# 例

OptionalもMaybeも型クラスである

Optional(Int)型はInt型の値か又はnilを持つ

Maybe IntegerはInteger型の値か又はNothingを持つ

#

そういうものを扱う方法を抽象化することが出来る

- Functor
- Applicative
- Monad

# 型

Functor

$(a → b) → (f a → f b)$

Applicative

$f (a → b) → f a → f b$

Monad

$m a → (a → m b) → m b$

# ここから臨時

# JavaScriptやRubyでは

動的に型が決まるので常にnilになる可能性がある

正しく？product関数を実装するには

```ruby
def product(x, y)
  if x && y # 本質
    x * y
  else      # では
    nil     # ない
  end       # コード
end
```

nilチェックを常にしなければならない

#

更に厳密にするなら

```ruby
def product(x, y)
  if x && y
    if x.respond_to? :*
      x * y
    else
      nil
    end
  else
    nil
  end
end
```

`*`演算が出来るかどうか確認する必要がある

#

しかしそれでもこのproduct関数を落とせる

```ruby
1 * :a
# TypeError: no implicit conversion of Symbol into Integer
#         from (irb):1:in `*'
#         from (irb):1
#         from /some/where/bin/irb:11:in `<main>'
```

関数呼び出し時に型がチェックされるわけではない

`y`に数値が渡ってくる保証がない

#

```ruby
def product(x, y)
  if x && y
    if x.respond_to? :*
      if y.instance_of? Fixnum || y.instance_of? Float
        x * y
      else
        nil
      end
    else
      nil
    end
  else
    nil
  end
end
```

これもうわかんねぇな

#

そもそもダメなときにnilを返すので良いのか？

悲しみを生み出す可能性を自分でまた苦労してまで生み出すべきなのか？

なので`product`関数は

```ruby
def product(x, y)
  x * y
end
```

とだけ実装出来て欲しい
