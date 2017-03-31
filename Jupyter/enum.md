
## F#と列挙体

F#の解説記事やドキュメント等の作成においてAzure Notebooksは活躍しまくりなんじゃないか説を検証するために、簡単なノートを作成してみようと思います。今回のテーマは列挙体です。F#における列挙体は判別共用体の陰に隠れてしまいがちですが、実務などでF#を使用する際にはどうしても必要になってきます。

#### 基本


```fsharp
// 列挙体の定義
type MyEnum =
    | One   = 1
    | Two   = 2
    | Three = 3
```


```fsharp
MyEnum.One
```




    One {value__ = 1;}




```fsharp
enum<MyEnum> 2
```




    Two {value__ = 2;}




```fsharp
enum<MyEnum> 4
```




    4 {value__ = 4;}




```fsharp
MyEnum() // コンストラクタ
```




    0 {value__ = 0;}



#### 列挙体を利用する関数の例

`int`関数を利用することで、数値として扱うことができます。


```fsharp
let next : MyEnum -> MyEnum = int >> ( + ) 1 >> enum
```


```fsharp
next MyEnum.One
```




    Two {value__ = 2;}




```fsharp
next MyEnum.Three
```




    4 {value__ = 4;}



#### フラグとしての列挙体


```fsharp
[<System.Flags>]
type MyBitFlags =
    | A = 0b0001
    | B = 0b0010
    | C = 0b0100
    | D = 0b1000
```


```fsharp
MyBitFlags.A ||| MyBitFlags.C
```




    A, C {value__ = 5;}



結果の表示が親切です。


```fsharp
enum<MyBitFlags> 6
```




    B, C {value__ = 6;}




```fsharp
enum<MyBitFlags> 0b1001
```




    A, D {value__ = 9;}




```fsharp
let x = MyBitFlags.A ||| MyBitFlags.B
```


```fsharp
x &&& MyBitFlags.A
```




    A {value__ = 1;}




```fsharp
x &&& MyBitFlags.C
```




    0 {value__ = 0;}



#### int32以外の型を使いたい！

サフィックスを付けて優しく定義するだけです。(uyはbyte型のサフィックス)


```fsharp
type ByteEnum =
    | One   = 1uy
    | Two   = 2uy
    | Three = 3uy
```


```fsharp
ByteEnum.One
```




    One {value__ = 1uy;}




```fsharp
byte ByteEnum.Two
```




    2uy




```fsharp
int ByteEnum.Three
```




    3



byte型の列挙体を定義するのは非常に簡単でしたが、少し困ることもあります。
enum関数を使用することができなくなります。


```fsharp
enum<ByteEnum> 1uy
```

    /home/nbuser/input.fsx(1,1): error FS0001: The type 'int32' does not match the type 'byte'

そんな時にご紹介したいのが、こちらの商品です。


```fsharp
LanguagePrimitives.EnumOfValue<byte, ByteEnum> 1uy
```




    One {value__ = 1uy;}



今ならなんと、`LanguagePrimitives.EnumToValue&lt;'Enum,'T&gt;`関数もお付けします！ (あまり使わない)

### あとがき

- 出力結果に型情報も欲しい
- 現時点だとノート名(ファイル名)に日本語を使えないっぽい
