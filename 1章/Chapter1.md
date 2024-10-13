## クラスと変数はキャメルケース 定数はスネークケース

複数の単語からなる複合語の単語の区切りを大文字で記述する形式を「キャメルケース」
キャメルケースの中でも、先頭の文字を小文字にしたものをキャメルケース（例：getPropertyName）

先頭の文字を大文字にしたものをパスカルケース（例：TextDataBinder）

Javaでは、次のルールで記述
・クラス名 →　大文字始まりのキャメルケース（パスカルケース）
・変数 →　小文字始まりのキャメルケース
一方、定数は、すべて大文字で、単語をアンダースコア（_）で区切る「スネークケース」の形式で記述

## 変数は名詞、メソッドは動詞で命名
```java
public class Status {
 // これは誤った命名
 // private boolean isStarted = false;

 // 正しい命名
 private boolean started = false;
 public boolean isStarted() {
 return this.started;
 }
 public void setStarted(boolean started) {
 this.started = started;
 }
}

```

## 「不吉な匂い」がする名前
役割が曖昧であったり、多くの役割を受け持ったりした結果、肥大化してしまったクラスに見られる
「不吉な匂いがするクラス名」というものがある

次のような名前がついたクラス
```java
・XxxInfo
・XxxData
・XxxManager
```
これらは、内容が詳細に決まっていなくても、なんとなく役割がわかるため、一見、便利な名前
しかし、その分、新たなデータや処理が必要になった場合に、なんでもかんでも詰め込まれることになり、結果、肥大化する可能性

## プリミティブ型
プリミティブ型は、真偽値型と数値型、文字型からなる基本的なデータ型
https://freelance-jak.com/technology/java/1175/

## 参照型
変数やメソッドをまとめる入れ物がクラス
このクラスという入れ物から作った実際のオブジェクトが、インスタンスである。
生成されたインスタンスを使用するには、そのインスタンスを特定する情報（住所のようなもの）を知っておく必要がある。その情報を、参照（ポインタ）と呼びます。そして、参照という値を保持する型のことを、参照型と呼ぶ

## ラッパークラス

プリミティブ型はオブジェクトではなく、単なる値でありメソッドを持ちません。しかし、プログラムの中では、プリミティブ型の値に対する操作（文字列との相互変換など）が必要になる場面がある
これをサポートするために、Javaはプリミティブ型を包み込み、その値を操作する機能を備えた「ラッパークラス」を提供
ラッパーとは、「包み込むもの」という意味

## ラッパークラスオブジェクトの作成

プリミティブ型からラッパークラスオブジェクトを作成するには、`valueOf()`メソッドを使います。`valueOf()`メソッドは静的メソッドで、受け取った値を持つラッパークラスのインスタンスを返す

```java
int value = 5;
Integer wrapper = Integer.valueOf(value);
```

コンストラクタを使ってインスタンスを作成することもできるが、Java 9以降では非推奨
その理由は、ラッパークラスは一度作成されるとその値を変更できず、同じ値を何度も使用する場合にオブジェクトを複数作成してしまい、メモリの無駄遣いになる可能性

## オートボクシングとアンボクシング

プリミティブ型のデータと参照型であるラッパークラスのオブジェクトは型が異なるため、基本的にお互いの演算や代入はできません。しかし、Java 5.0からはこれらの間で自動的に変換が行われる
プリミティブ型からラッパークラスへの自動変換を**オートボクシング**、ラッパークラスからプリミティブ型への変換を**アンボクシング**という

```java
int num = 10;
Integer numInt = 10; // コンパイル時に Integer.valueOf(10) に自動変換される（オートボクシング）
Integer sum = num + numInt; // numInt が numInt.intValue() で int に自動変換され（アンボクシング）、演算結果を再度オートボクシングする
```

## メリットとデメリット

この自動変換には、変換処理を書かなくてもよいというメリット
一方で、意図しない変換が発生し非効率な処理になったり、参照型であるラッパーオブジェクトを誤って`==`演算子で比較し意図しない結果になったりするデメリット

```java
Integer num1 = new Integer(3);
Integer num2 = new Integer(3);
System.out.println(num1 == num2); // false（num1 と num2 は別のオブジェクトであるため）
System.out.println(num1.equals(num2)); // true
```




### オートボクシングの注意点

オートボクシングはラッパー型の`valueOf`メソッドを用いて行われ、-128～127の範囲の値には事前に生成されたオブジェクトが利用
そのため、この範囲内の値をオートボクシングしたオブジェクトは常に同一のオブジェクトになるが、その範囲外の値では異なるオブジェクトになる。
この動作仕様を理解しないと、コード内でどこがプリミティブ型でどこがラッパーなのかがわからなくなり、バグの原因となる可能性

- **オートボクシング、アンボクシングの利用を控える**
  - 原則として、オートボクシングやアンボクシングは使用せず、明示的な変換を行う。

- **ラッパークラスの利用**
  - ファイル、データベース、HTTPリクエストなどを保持する値には、ラッパークラスを使用する。

- **数値計算におけるプリミティブ型の利用**
  - 数値計算に用いる変数は、プリミティブ型とする。

- **オートボクシング、アンボクシングの例外的利用**
  - 記述量の削減が効果的な場合に限り、オートボクシングやアンボクシングを利用する。
 

## クラスを定義する
クラスを定義するには、classキーワードを使い、クラスには、フィールドとメソッドを定義することができる。次
```java
public class SampleClass { // クラス
 private String name = "Sample"; // フィールド
 public String action() { // メソッド
 return name + "> " + "Action";
 }
}

```


## パッケージ
Javaでは多数のクラスを定義することになるため、アプリケーションやアプリケーション内部の機能などでクラスを整理する必要がある。
そのような場合に、クラスを階層的に整理するために使うのがパッケージ
```java
├── application
│   ├── external
│   ├── query
│   │   └── user
│   │       ├── UserQueryModel
│   │       └── UserQueryService
│   └── service
│       └── user
│           ├── UserApplicationService
│           └── UserGetOutput
├── domain
│   └── model
│       └── user
│           ├── User
│           └── UserRepository
├── infrastructure
│   ├── external
│   ├── mysqlrepository
│   │   └── user
│   │       └── UserMySQLRepository
│   └── mysqlquery
│       └── user
│           └── UserMySQLQueryService
└── interfaces
    └── api
        └── user
            ├── UserController
            ├── UserPostRequestBody
            └── UserGetResponseBody
```
パッケージを利用することで、アプリケーションの構成が明確になり、クラスの役割をよりはっきりと示すことができる。ただし、パッケージの命名には注意が必要

一般的な慣習として、他者が提供するライブラリやフレームワークとパッケージ名が重複しないように、自分が所有しているドメイン名を逆にしたものからパッケージ名を始めることが推奨されている。

例として、acroquest.co.jpを逆にした「jp.co.acroquest」というパッケージ名を使用する。また、パッケージ名とクラス名を組み合わせた名前（完全修飾クラス名＝FQCN）が重複すると、プログラムが意図しない動作をする可能性がある。

特に複数の開発者が関与するプロジェクトでは、このFQCNの重複を避けるために、機能やモジュールごとにパッケージ名を分けることが望ましい。


## instanceof演算子
Javaでは変数の型が明確だが、変数の型と完全に一致するクラスだけでなく、その型を継承したサブクラスでも変数に代入することができる。

たとえば、変数をObjectで定義していた場合、その変数にはStringでもIntegerでも代入することが可能であり、変数に代入されたオブジェクトの型が、実際に何であるかを判定する方法として、insanceof演算子を利用するができる

instanceof演算子は、左辺のオブジェクトが右辺で指定したクラス（または継承したクラス）であるかどうかを判定することができる


```java
public interface BaseService {
 public String say();
}
public abstract class AbstractBaseService implements BaseService {
 protected String name;
 public AbstractBaseService(String name) {
 this.name = name;
 }
}
public class FooService extends AbstractBaseService {
 public FooService(String name) {
 super(name);
 }
 @Override
 public String say() {
 return "Hello!";
 }
}

Object obj = new FooService("hello");
System.out.println(obj instanceof FooService); // true
System.out.println(obj instanceof AbstractBaseService); // 親クラスなので true
System.out.println(obj instanceof BaseService); // インタフェースを実装しているので true
System.out.println(obj instanceof Integer); // 継承関係がないので false
if (obj instanceof FooService) {
 FooService service = (FooService) obj; // obj は FooService であるため、キャストが可能
 System.out.println(service.say()); // Hello

```


## オブジェクトの等価性
オブジェクトの値が同じかどうかを判定したい場合には、オブジェクトのequalsメソッドを利用する
```java
@Override
 public boolean equals(Object obj) {
 // このオブジェクトと引数で渡された obj の内容が等しければ true を返し、
// 異なれば false を返す
 }
```

### hashCodeメソッド
hashCodeメソッドは、自身のオブジェクトの内容を表す数値（ハッシュ値）を返すメソッド

ハッシュ値は、オブジェクトの値を一定のルールに従って数値化したものであり、同じ値を持つオブジェクトは同じハッシュ値となります。逆に、ハッシュ値が異なる場合は異なるオブジェクトとなります。

しかし、同じハッシュ値だからといって、同じ値を持つオブジェクトであるとは限りません。まとめると、hashCodeメソッドによるハッシュ値には、次の性質があります。

・同じオブジェクトのハッシュ値は、必ず同一となる

・ハッシュ値が異なる場合には、異なるオブジェクトである

・異なるオブジェクトでもハッシュ値が同じになることがある


```java
 @Override
 public int hashCode() {
 // このオブジェクトの内容を表す数値を返す
 }
```
なぜ、equalsだけではなくhashCodeメソッドによるハッシュ値が必要になるのか。
「オブジェクトが等価かどうかはequalsメソッドで判定できるが、equalsメソッドは値を1つずつ検証するため、計算に時間がかかる。

対して、ハッシュ値であれば、一定の計算と、計算結果（int）の比較のみで済むために、より高速にオブジェクトが等価である、または等価でないことを判定できます。そのため、後述するjava.util.HashMapやjava.util.HashSetなどでは、


・最初にハッシュ値でオブジェクトを比較する

・ハッシュ値が等しい場合に限り、equalsメソッドで厳密な判定をおこなう


という流れでオブジェクトを比較します。
2つのオブジェクトが等価だと判定するためには、次の2つの条件を満たさなければなりません。このルールを守らなかった場合、バグを生む可能性があります。

・オブジェクトのhashCodeメソッドの値が同一である


・equalsメソッドで等価だと判定される

## 列挙型（enum）
public static finalによる定数があるが、public static finalによる定数には「型安全ではない」という問題ががる。

型安全とは、変数に型を割りつけることで、不正な動作を防ぐこと

たとえば、String型の定数として、次の3つを定義


```java
public static final String COLOR_BLUE = "blue";
public static final String COLOR_YELLOW = "yellow";
public static final String COLOR_RED = "red";
そして、この定数を利用することを想定したprocessColorメソッドを定義し、引数にcolorを定義したとします。
public void processColor(String color) {
 // 引数を利用した処理
}
```


ここでprocessColorメソッドにはCOLOR_BLUE、COLOR_YELLOW、COLOR_REDのいずれかが渡される想定が、

もし開発者がその想定を十分に把握していなかったり、コーディングミスをしてしまい、colorの値として"green"を渡してしまう可能性

Javaではコンパイルの際に、定数を利用しているクラスの定数に、定数を定義しているクラスの定数の値が展開されます。

そのため、定数クラス側で定数の値を変更してコンパイルしても、利用クラス側の定数の値は変わらない

定数クラス側をコンパイルした場合には、利用クラスも一緒にコンパイルする必要があります。

public String color = SELECTED_COLOR;

このクラスをコンパイルすると、利用クラス側では次のソースコードと同じ状態になります。
```java
public String color = "blue";
```
そして、この状態で定義クラス側の定義をSELECTED_COLORを"red"と変更してコンパイルしても、利用クラス側は"blue"のままとなってしまうのです。

利用クラス側も同時にコンパイルすれば、値は"red"に変わります。

このような問題を解決するために、Java 5.0から導入されたのがenum型（列挙型）
```java
public enum TaskType {
 PRIVATE, WORK
};

public class Task {
 private String id;
 private TaskType type; // enum 型の TaskType として宣言する
 private String body;
 public Task(TaskType type, String body) {
 this.id = UUID.randomUUID().toString();
 this.type = type;
 this.body = body;
 }
 public String getId() {
 return id;
 }
 public TaskType getType() {
 return type;
 }
 public void setType(TaskType type) {
 this.type = type;


 public String getBody() {
 return body;
 }
 public void setBody(String body) {
 this.body = body;
 }

Taskクラスを利用する側のコードは、次のようになります。このように、enum型はswitch文にも利用できます。


 Task task = new Task(TaskType.PRIVATE, "buy milk");
 TaskType type = task.getType();
 System.out.println(TaskType.PRIVATE.equals(type)); // true
 switch (type) {
 case PRIVATE: // TaskType. がつかないことに注意
 // TaskType. をつけるとコンパイルエラーになる
 System.out.println("Task[type = " + type + "]"); // 文字列として出力可能
 break;
 case WORK:
 System.out.println("Task[type = " + type + "]");
 break;
 }
}
```

enum型はクラスの一種であるため、別のenumの値は代入できず、型安全が保証されます。こ









