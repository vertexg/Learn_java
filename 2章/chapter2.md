## 配列
```java
int[] array1 = new int[10]; // ①大きさだけを指定

int[] array2 = { 1, 2, 3, 4, 5 }; // ②初期値を指定

int[] array3 = new int[] { 10, 9, 8, 7, 6 }; // ③初期値と型を指定

System.out.println(array1.length); // length で長さを取得できる
System.out.println(array2.length);
System.out.println(array3.length);

///// 10 5 5
```
intやdoubleなどのプリミティブ型の配列を初期化した場合、値はすべて「0」（小数点ありのfloatやdoubleでは0.0）の配列StringやIntegerなどのオブジェクトでは値がすべて「null」の配列になる

、どのような用途で、どの初期化方法を選ぶといいか

（1）宣言時に内容が決まっていない　→　newで要素数のみ指定する
  
【例】int[] array = new int[10];

（2）宣言時に内容が決まっている　→　値の一覧を列挙する
  
【例】int[] array = {1, 1, 2, 3, 5};

配列で複数のデータを扱う

（3）宣言後に内容が決まる、もしくは引数として利用する
  
　　→newの宣言をつけて値の一覧を列挙する

【例】array = new int[] {1, 1, 2, 3, 5};

（1）のように配列の代入する値が宣言時に決まっていない場合には、後述するコレクションを利用することのほうが多い。
  
配列を宣言する際には、初期化時に値を決める（2）や（3）の書き方を覚えておくとよい

## 配列のサイズを変更
配列は、一度作成してしまうと要素数を変更することができない。そのため、すでに作成した配列の要素数を変更したい場合には、新しい配列を作成したうえで、古い配列から新しい配列に必要な情報をコピーする必要

配列をコピーする手段として、Java 5.0まではSystemクラスのarraycopyメソッドを、Java 6以降ではArraysクラスのcopyOfメソッドを利用
```java
int[] array = { 1, 1, 2, 3, 5, 8, 13 };
int[] newArray1 = new int[array.length + 3];
System.arraycopy(array, 0, newArray1, 0, array.length); // ① Java 5.0 までの配列コピー方法
int[] newArray2 = Arrays.copyOf(array, array.length + 3); // ② Java 6 以降の配列コピー方法
newArray1[7] = 21;
newArray1[8] = 34;
newArray1[9] = 55;
newArray1[10] = 89; // ArrayIndexOutOfBoundsException が発生

```
Systemクラスのarraycopyメソッドの引数

1. **コピー元の配列**: コピーを行う元の配列。
2. **コピー元の配列の何番目からコピーするか**: コピーを開始するインデックス。
3. **コピー先の配列**: コピーした要素を格納する配列。
4. **コピー先の配列の何番目にコピーするか**: コピー先の配列で要素を挿入する開始インデックス。
5. **コピーする配列の要素数**: コピーする要素の数。


## 配列の文字列変換
```java
int[] array = { 1, 1, 2, 3, 5, 8, 13 };
System.out.println(array);
System.out.println(Arrays.toString(array));

[I@1bb60c3
[1, 1, 2, 3, 5, 8, 13]
1行目の[I@1bb60c3の部分は、それぞれ以下を示しています。
・[　→　配列
・I　→　int型
・@以降の文字列　→　ハッシュ値
```
Arraysクラスのsortメソッドによるソートの並び順は、並び替えの対象によって変わる。

・プリミティブ型 →　値の昇順で並び替え

・オブジェクト →　ComparableインタフェースのcompareToメソッドを用いて並び替え

```java
Integer[] array = { 3, 1, 13, 2, 8, 5, 1 };
Comparator<Integer> c = new Comparator<Integer>() {
 @Override
 public int compare(Integer o1, Integer o2) {
 return o2.compareTo(o1);
 }
};
Arrays.sort(array, c);
System.out.println(Arrays.toString(array));
▼
[13, 8, 5, 3, 2, 1, 1]
compareメソッドの戻り値によって、並び替えの挙動が変わります。
・戻り値が0以上の場合 →　第1引数→第2引数の順に並び替え
・戻り値が0未満の数値を返した場合 →　第2引数→第1引数の順に並び替え

```

## Comparatorか、Comparableか？

上の例ではComparatorを利用してソートをおこないましたが、ソートをおこなうもう1つの方法として、ソート対象となるクラス自身をComparableインタフェースの実装クラスとして定義して、compareToメソッドを実装する方法

以下に、StudentクラスをComparableインタフェースの実装とした場合の例。


```java
class Student implements Comparable<Student> {
 private String name;
 private int score;
 public Student(String name, int score) {
 this.name = name;
 this.score = score;
 }
 public int getScore() {
 return score;
 }
 // 略
 @Override
 public int compareTo(Student o) {

 return Integer.compare(o.getScore(), getScore());
 }
```

Comparableを使用することで、ArraysクラスのsortメソッドにComparatorを指定せずにソートすることが可能である。しかし、Comparableは1種類のソートしか提供できないため、複数の異なるソート基準を切り替えることができない。

上記の例では点数の降順でソートしているが、たとえば名前の昇順や点数の昇順など、別の基準でソートしたい場合にはComparatorが必要となる。
Comparableによるソートは、そのクラス自身が持つデフォルトの並び順によるものであるため、最も自然な並び方であるべきである。

たとえば、数字なら昇順、文字列ならASCIIコードの昇順に並ぶことが期待される。Studentクラスの場合、「生徒」を並べる際には、「出席番号の昇順」や「名前の昇順」が基本的な並べ方であり、「点数の降順」などは特殊な並び方といえる。
このように、次のような使い分けができる。

Comparableによるソート: そのクラス自身の最も自然な並び方によるデフォルトソートとして利用する。

Comparatorによるソート: 業務的に必要な並び方によるソートとして利用する。



## 配列の検索

（1） 事前にArraysクラスのsortメソッドを用いてソートする

（2） ArraysクラスのbinarySearchメソッドは利用せずに線形検索などをおこなう

ソートの処理にも時間がかかりますから、場合に応じて以下のように処理したほうが、よりパフォーマンスが良くなる

・同じデータに対して何度も検索をする場合

→（1）のように事前にソートしてから検索する

・一度だけしか検索をしない場合

→（2）のようにソートをせずに検索だけしてしまう




## 可変長引数を使ったメソッド定義

可変長引数は、メソッドの引数として配列を指定する代わりに、型の後ろに「...」（ドットを3つ）をつけて定義する。これにより、メソッドを呼び出す側は配列を毎回生成する必要がなくなる。

#### 例: 可変長引数によるメソッド定義

```java
void log(String message, String... args) {
    System.out.println(message);
    System.out.println(" パラメータ：");
    for (String arg : args) {
        System.out.println(arg);
    }
}
```
### 可変長引数を使わない例
以下は、可変長引数を使用せずに配列を引数として渡す方法の例
```java
public class Example {
    public static void main(String[] args) {
        // 配列を生成して渡す
        log("ユーザを登録しました", new String[]{"userName", "Ken"});
        log("エラーが発生しました", new String[]{"Cannot load file"});
        log("処理を終了しました", new String[0]);
    }

    static void log(String message, String[] args) {
        System.out.println(message);
        System.out.println("パラメータ：");
        for (String arg : args) {
            System.out.println(arg);
        }
    }
}
### 可変長引数を使った例

public class Example {
    public static void main(String[] args) {
        // 可変長引数を使用
        log("ユーザを登録しました", "userName", "Ken");
        log("エラーが発生しました", "Cannot load file");
        log("処理を終了しました");
    }

    static void log(String message, String... args) {
        System.out.println(message);
        System.out.println("パラメータ：");
        for (String arg : args) {
            System.out.println(arg);
        }
    }
}

```
### 可変長引数のメリット

可変長引数を使うことで、メソッドの呼び出しがシンプルになり、コードが読みやすくなる。従来は配列を渡すために毎回 `new String[]` を記述する必要があったが、それが不要になる。

#### 例: 可変長引数のメソッド呼び出し

```java
log(" ユーザを登録しました ", "userName", "Ken");
log(" エラーが発生しました ", "Cannot load file");
log(" 処理を終了しました ");
```

### コンパイル時の動作

可変長引数はコンパイル時に配列に変換される。そのため、メソッド定義側では従来の配列を使ったときと同様に処理できる。

#### 例: コンパイル時の変換

以下のように、可変長引数の呼び出しはコンパイル時に配列として扱われる。

```java
log(" ユーザを登録しました ", new String[]{"userName", "Ken"});
log(" エラーが発生しました ", new String[]{"Cannot load file"});
log(" 処理を終了しました ", new String[0]);
```

このように、可変長引数を利用することで、コードの記述が簡潔になり、可読性も向上する。

## コレクションフレームワーク


| **名称**           | **概要**                                                                                   |
|--------------------|--------------------------------------------------------------------------------------------|
| 配列               | 複数の要素を扱うしくみ。シンプルだが柔軟性に欠ける。                                      |
| `java.util.List`   | 配列と同様に複数の要素を扱うことができ、インデックスを指定して値の取得や設定ができる[4]。       |
| `java.util.Set`    | `List`と似ているが、要素が重複した場合は登録しないため、重複のない複数の要素を扱うことができる。順序性がないため、インデックスを指定して値を取得・設定することができない。 |
| `java.util.Map`    | キーと値を利用して要素を扱うことができる。                                               |


・配列のようにインデックスを指定して値の取得や設定をしたい場合

→Listインタフェース

・要素に値の重複がない場合／検索・ソートを高速におこないたい場合

→Setインタフェース

・キーと値を分けて要素を扱いたい場合

→Mapインタフェース







