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

```java
List<String> names = new ArrayList<>();
names.add("Ken"); // ①値の追加
names.add("Shin");
names.add("Takuya");
System.out.println(" ①リストの中身：" + names.toString());
names.add(2, "Satoshi"); // ②値の挿入
System.out.println(" ②リストの中身：" + names.toString());
names.set(0, "Makoto"); // ③値の置換
System.out.println(" ③リストの中身：" + names.toString());
String thirdName = names.get(2); // ④値の取得
System.out.println(" ④ 2 番目の要素：" + thirdName);
names.remove(1); // ⑤値の削除
System.out.println(" ⑤リストの中身：" + names.toString());
int size = names.size(); // ⑥要素数の取得
System.out.println(" ⑥要素の数：" + size);

int takuyaIndex = names.indexOf("Takuya"); // ⑦値の検索
System.out.println(" ⑦ Takuya の位置：" + takuyaIndex);
boolean exists = names.contains("Shin"); // ⑧値が含まれているかの判定
System.out.println(" ⑧ Shin が含まれているか：" + exists);
```

## Listを検索する
Listを検索するためには、CollectionsクラスのbinarySearchメソッドを利用

```java
List<Integer> values = Arrays.asList(1, 1, 4, 5, 7, 8, 11, 12, 17, 21, 24);
int found = Collections.binarySearch(values, 5); // ①「5」という数字を検索
System.out.println(found);
int notFound = Collections.binarySearch(values, 6); // ②「6」という数字を検索
System.out.println(notFoun

```
同様の繰り返し処理をおこなう手段として、Iteratorインタフェースがある。Listインタフェースのiteratorメソッドを実行することで、このIteratorインタフェースを取得できる。

Iteratorインタフェースには、次のメソッドが用意されている

・次の要素があるかどうかを確認するhasNextメソッド

・次の要素を実際に取得するnextメソッド

次のソースコードはIteratorインタフェースを用いたもので、for-each文を使った場合と同じ実行結果
```java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
for (Iterator<String> iterator = list.iterator(); iterator.hasNext(); ) {
 String element = iterator.next();
 System.out.println(element);
}
```
for-each文を使ったほうがシンプルだが、イテレータを利用しなくてもいいように見える。しかし、イテレータには要素を削除するメソッドが用意されており、繰り返し処理をしながら、コレクションから要素を削除できる


```java
class Student {
 private String name;
 private int score;
 public Student(String name, int score) {
 this.name = name;

 this.score = score;
 }
 public String getName() {
 return name;
 }
 public int getScore() {
 return score;
 }
}

```


```java
List<Student> students = new ArrayList<>();
students.add(new Student("Ken", 100));
students.add(new Student("Shin", 60));
students.add(new Student("Takuya", 80));
Iterator<Student> iterator = students.iterator();
while (iterator.hasNext()) {
 Student student = iterator.next();
 if (student.getScore() < 70) {
 iterator.remove(); // 点数が 70 未満の人はリストから削除
 }
}
for (Student student : students) {
 System.out.println(student.getName() + ":" + student.getScore());
}
```
## Listの3つの実装クラス



## ArrayListクラス

## ArrayListクラスについて

**ArrayListクラス**は、内部に配列を持つクラスである。シンプルで扱いやすい実装であるため、利用頻度が高いクラスである。

### 特徴

- **内部に配列を持つ**: ArrayListは内部に配列を保持しており、追加した要素はすべて配列内に保持される。
- **高速なアクセス**: 要素のインデックスを指定して値を代入する処理や、値を取得する処理を高速に実行できる。
- **初期容量**: ArrayListが持つ配列の長さはコンストラクタで指定することができるが、指定しない場合は長さが10となる。

### 配列の拡張

- **配列のサイズ以上の要素を追加**: 配列のサイズ以上の要素を追加しようとした際には、「よりサイズの大きい配列を新たに生成して、元の配列からすべての要素をコピーする」という処理が実行される。
- **初期値の指定**: ArrayListに格納する配列の要素数のおおよその目安がついている際には、コンストラクタで初期値を指定して、配列生成、コピー処理の回数を削減することが推奨される。

### パフォーマンス

- **高速なアクセス**: インデックスを指定して要素を代入／取得する処理を高速におこなうことができる。
- **末尾への追加**: 末尾に要素を追加する処理も、問題ない速度で実行できる。
- **リスト中間への追加/削除**: リストの途中に要素を追加／削除する処理は、あまり高速にできません。配列の途中に要素を追加しようとすると、それ以降の要素をすべて1つずつ後ろへずらさなければいけないため、その処理に時間がかかってしまう。

- **高速なアクセス**: インデックスによるアクセスや末尾への追加は高速である。
- **低速な挿入/削除**: リスト中間への挿入や削除は、他の要素をシフトする必要があるため、パフォーマンスが低下する可能性



```java
import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();

        // 要素の追加
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");

        // 要素の取得
        String fruit = list.get(1); // "Banana"

        // 要素の設定
        list.set(1, "Blueberry");

        // 要素の削除
        list.remove("Apple");

        // サイズ取得
        int size = list.size(); // 2
    }
}
```

このようにArrayListクラスは柔軟性と使いやすさから、多くのJavaプログラムで利用されている。特に、大量のデータを動的に扱う必要がある場合に有用である。

![スクリーンショット 2024-10-15 8 09 16](https://github.com/user-attachments/assets/32812cb7-0e48-4c64-9d3e-ab9c19b1fef5)





## LinkedListクラス


、要素自身が前後の要素の情報を持つことでリストを構成しているクラスである。ArrayListと動作面での違いがないが、性能の特性に違いがあるため用途によって使い分けることが推奨

### 構造

- **要素のリンク**: 各要素は、自分の1つ前と1つ後の要素の情報（リンク）を持っている。例えば、「A」「B」「C」「D」という4つの要素がある場合、次のようになる。
  - 「A」は、「B」へのリンクを持っている。
  - 「B」は、「A」と「C」へのリンクを持っている。
  - 「C」は、「B」と「D」へのリンクを持っている。
  - 「D」は、「C」へのリンクを持っている。

### 性質

- **初期サイズの概念がない**: ArrayListのような初期サイズの概念はなく、要素が増えるたびにリンクを更新するだけ
- **性能の特性**: LinkedListはArrayListとほとんど逆の性質を持っている。リストの途中に要素を高速に追加／削除できる一方で、インデックスを指定して要素を代入／取得する処理には時間がかかってしまう。

### 例

- **リストの途中への要素追加**: 「A」「B」「C」「D」という4つの要素に対して、「B」と「C」の間に「E」を追加する。次の手順で追加する。
  1. 「B」のリンクを「C」から「E」に変更する。
  2. 「C」のリンクを「B」から「E」に変更する。
  3. 「E」は「B」と「C」にリンクする。

このように、ArrayListのようにそれ以降の要素すべてを更新する必要がないため、リストの途中への要素の追加や削除が高速にできる
![スクリーンショット 2024-10-15 8 11 14](https://github.com/user-attachments/assets/5ffc1f71-9b8e-4b33-a27c-6cabd708256a)

### 使用例

```java
import java.util.LinkedList;

public class Main {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        list.add("D");

        // リストの途中への要素追加
        list.add(2, "E");

        System.out.println(list); // [A, B, E, C, D]
    }
}
```

この例では、LinkedListの`add()`メソッドを使用してリストの途中への要素追加を実行している。


## CopyOnWriteArrayListクラス

**CopyOnWriteArrayListクラス**は、複数のスレッドから同時にアクセスしても正しく処理が行われる、ArrayListクラスの拡張である。スレッドとは、プログラムの実行の流れを表す単位であり、複数のスレッドを作ることで、別々の処理を並行で実施できる。

### ArrayListの問題点

- **ConcurrentModificationException**: ArrayListクラスでは、あるスレッドでiteratorやfor-each文を使ったループを実行している際に、別のスレッドがそのArrayListオブジェクトの要素を変更すると、ConcurrentModificationExceptionが発生

### CopyOnWriteArrayListの特徴

- **スレッドセーフ**: CopyOnWriteArrayListクラスは、iteratorやfor-each文を使ったループを行う際に、元のリストのコピーを作成し、そのコピーに対してループを行う。そのため、他のスレッドがリストの操作を行っても、ループ側のリストに影響が出ず、例外は発生しない
- **パフォーマンス**: CopyOnWriteArrayListクラスは、ArrayListクラスとほぼ同等の性能が出るが、要素を追加／変更／削除する時に内部的に持っている配列をコピーするため、ArrayListクラスと比較すると性能は悪くなる

### 使用例

- **複数のスレッドからのアクセス**: 1つのリストオブジェクトに対し、複数のスレッドが同時にアクセスする可能性のある場合は、CopyOnWriteArrayListクラスを使用する

### 他のList実装クラス

- **UnmodifiableListクラス**: リストオブジェクトを読み取り専用にするUnmodifiableListクラス（CollectionsクラスのunmodifiableListメソッドで生成）などがある

### 例

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class Main {
    public static void main(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");

        // 別のスレッドでリストを操作する
        Thread t = new Thread(() -> {
            list.add("D");
            list.remove("A");
        });
        t.start();

        // ループを行う
        for (String s : list) {
            System.out.println(s);
        }
    }
}
```

この例では、CopyOnWriteArrayListクラスを使用して、複数のスレッドから同時にアクセスしても正しく処理が行われることを示している。


## Listの実装クラスの使い分け
![スクリーンショット 2024-10-15 9 02 32](https://github.com/user-attachments/assets/5fabffe0-cb62-4d4e-9af7-cc37bce02054)

キーの大小を意識した部分集合を取り扱う場合 →　TreeMap

・要素の順序を保持する必要がある場合 →　LinkedHashMap

・複数スレッドから同時にアクセスする場合 →　ConcurrentHashMap

・その他の場合 →　HashMap

## Set
Setインタフェースは、値の集合を扱うことができるインタフェース
```java
Set<String> names = new HashSet<>();
names.add("Ken"); // ①値の追加
names.add("Shin");
names.add("Takuya");
System.out.println(" ① Set の中身：" + names.toString());
names.add("Shin"); // ②値の上書き
System.out.println(" ② Set の中身：" + names.toString());
names.remove("Shin"); // ③値の削除
System.out.println(" ③ Set の中身：" + names.toString());
int size = names.size(); // ④要素数の取得
System.out.println(" ④要素の数：" + size);
boolean existKen = names.contains("Ken"); // ⑤値の検索
System.out.println(" ⑤ Ken の存在：" + exist

```


## 例外
### try～catch～finally
```java
try {
 // SomeException 例外が発生するコードを含む処理
} catch (SomeException ex) {
 // SomeException 例外を catch した場合の処理
} finally {
 // try ～ catch ブロックを終了する際に必ず実行するべき処理
}

例
InputStream is = null;
try {
 is = Files.newInputStream(path);
 read(contents);
 // contents に対する処理
} catch (IOException ex) {
 // 例外を捕捉した場合の処
} finally {
 if (is != null) {
 try {
 is.close();
 } catch (IOException closeEx) {
 // is.close メソッドが IOException を throw するため
 // catch ブロックが必要だが、この場面で処理する
 // ことがないため、何もしない
 }
 }
}

```

## try-with-resources
finallyブロックの書き方は冗長で、リソースを複数使用するtry～catchブロックでは冗長さがさらに倍増し、非常に面倒。
try-with-resourcesをつかうことで簡単になる

上のコードを書き換えると

```
try (InputStream is = Files.newInputStream(path)){
 is.read(contents);
 // contents に対する処理
} catch (IOException ex) {
 // 例外を捕捉した場合の処理
}
```
なぜこの書き方がOKなのか？

Java 7から、InputStreamなどのリソースを扱うクラスは、java.lang.AutoCloseableインタフェースまたはjava.io.Closeableインタフェースを実装するようになり、

そして、tryブロックの開始時にAutoClosableインタフェースの実装クラスを宣言しておくと、そのtry～catchブロック終了時の処理をおこなうcloseメソッドを自動的に呼び出してくれるようになったため。

closeメソッドで例外が発生した場合はどうなるか？

その場合は、try-with-resourcesブロックの外に例外がthrowされます。しかし、その前にtryブロックで例外が発生している場合は、closeメソッドの例外は抑制され、tryブロックで発生した例外がthrowされることになります。抑制された例外は、ThrowableクラスのgetSuppressedメソッドで取得できる


## マルチキャッチ

```java
try {
 Class<?> clazz = Class.forName(className);
 SomeClass objSomeClass = clazz.newInstance();
} catch (ClassNotFoundException ex) {
 // ClassNotFoundException に対するエラー処理
} catch (InstantiationException ex) {
 // InstantiationException に対するエラー処理

```
このようなcathcブロックだと、例外が増えるたびにcatchブロックが必要になるため、例外処理だけでプログラムが煩雑になる

しかし、7から導入された「マルチキャッチ」を利用すると、catchブロックの記述を少し楽にすることができる

```java
try {
 Class<?> clazz = Class.forName(className);
 SomeClass objSomeClass = clazz.newInstance();
} catch (ClassNotFoundException |
 InstantiationException |
 IllegalAccessException ex) {
 // エラー処理
}
```










