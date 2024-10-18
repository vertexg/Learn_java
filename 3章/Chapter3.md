## プリミティブ型の値渡しと参照型の値渡し
###  　プリミティブ型と参照型の値の渡し方

```java
public class CallByValueSample {
 public static void main(String... args) {
 int value = 1;
 callByValue(value);
 System.out.println(" 呼び出し元：" + value);
 }

 public static void callByValue(int value) {
 value++;
 System.out.println(" 呼び出し先：" + value);
 }
}

呼び出し先：2
呼び出し元：1
```
### 参照型
```java
public class Entity {
 public int value;
}
public class CallByReferenceSample {
 public static void main(String... args) {
 Entity entity = new Entity();
 entity.value = 1;
 callByReference(entity);
 System.out.println(" 呼び出し元：" + entity.value);
 }

 public static void callByReference(Entity entity) {
 entity.value++;
 System.out.println(" 呼び出し先：" + entity.value);
 }
}
```
参照型の場合は、オブジェクトそのものではなく、オブジェクトへの「参照」を表す値を渡すため、呼び出し先のメソッドでおこなった変更が、呼び出し元に反映されるという特徴

## Javaが使える可視性
Javaの変数やメソッドを利用できる範囲のことを「可視性」と呼ぶ。可視性を適切に設定することで、誤使用を減らしたり、拡張性を高くすることができる。

### 可視性のグッドプラクティス
可視性を適切に設定することが重要 可視性は、広ければ広いほどほかのクラスから利用しやすくなりますが、逆にいえば思わぬクラスから
呼び出されてしまう可能性も高くなる


### 原則、最も範囲が狭い可視性にする
publicにすると、別のクラスから呼び出されたり、変数の値を書き換えたりできてしまう。リスクを下げるために、範囲が狭い可視性を使用。原則として、

・クラスで宣言するフィールドは、

・外部からアクセスするメソッドのみ、publicにする



## 拡張性を上げるためにprotectedにする
何でもprivateにすると、使いづらくなり,機能を拡張をする場合に、privateなメソッドだと、継承しても書き換えることができない。
そのため、拡張する可能性のあるメソッドはprotectedにしておき拡張性を上げる。

```java
public class XxxDataLoader {
 private String fileName;

 private List<String> load() throws IOException {
 List<String> lines = Files.readAllLines(Paths.get(this.fileName));
 return lines;
 }

 …
}
public class YyyDataLoader extends XxxDataLoader {
 // コンパイルエラーとなる
 // protected List<String> load() throws IOException {
 // …
 // }
}

```

## オブジェクトのライフサイクル

Javaのオブジェクトの生存期間のことを「ライフサイクル」と呼び,平たくいえば、オブジェクトが生成されてから、破棄されるまでの期間

（1） ローカル変数
ローカル変数は、処理のブロック内でのみ利用可能な変数です。変数を宣言した箇所で生成され、ブロックが終了した時点で破棄される

（2） インスタンス変数
インスタンス変数は、クラスのフィールドとして宣言する変数　インスタンス変数のライフサイクルは、親のインスタンスと同一、つまり、親オブジェクトの生成時に生成され、親オブジェクト
がガベージコレクションされる時に一緒に破棄される
（3） クラス変数
クラス変数は、クラスのstaticフィールドとして宣言する変数。Javaの変数の中で最も長いライフサイクルを持つ。クラスロード時に生成され、クラスアンロード時に破棄されます。多くの場合、
Javaのプロセス起動時に生成され、プロセス終了時に破棄されます。なお、インスタンス変数とクラス変数には、可視性（アクセス修飾子）を指定することができる。
```java
public class LifeCycleSample {
 public static int classVariable = 1; // クラス変数
 public int instanceVariable = 1; // インスタンス変数

 public void someMethod() {
 // メソッド内で使えるローカル変数
int localVariable = 1;

 if (instanceVariable > 0) {
 // if ブロックの中で使えるローカル変数
 int localSubVariable = 1;

 …

 } // localSubVariable はここで破棄される

 …

 } // localVariable はここで破棄される
}
```
## ライフサイクルのグッドプラクティス

### ライフサイクルを短くして事故を防ぐ
ライフサイクルは、長ければ長いほど、意図せず値が書き換えられてしまう可能性が大きなる

書いてはいけないパターン

```java
EmployeeServiceインスタンスに対してidやname、birthの値をセットしてから、createメソッドを呼んでファイルに保存する形になっている。
値をセットしてからファイルに保存するまでの間、EmployeeServiceインスタンス内に値を保持しておく必要があります。このため、idやname、birthの値をセットした後、
createメソッドを呼ぶ前に、すぐに別の処理が割り込んで値の書き換えを実行されてしまった場合、元の処理では意図しない値がファイルに書き込まれ、バグとなってしまう。



public class EmployeeService {
 private int id;
 private String name;
 private LocalDate birth;
 // setter ／ getter は省略

 public void create() {
// id、name、birth の値をファイルに保存する
 }

 public void get(int id) {
 // 指定された id と合致するデータをファイルから読み取り、
 // id、name、birth に値をセットする
 }
}
public void MainService {
 private EmployeeService employeeService = new EmployeeService();

 public void register() {
 this.employeeService.setId(1);
 this.employeeService.setName(" 佐藤 ");
 this.employeeService.setBirth(LocalDate.of(1980, 2, 7));
 this.employeeService.create();
 }

 public void show() {
 this.employeeService.get(1);
 System.out.println(this.employeeService.getName());
 System.out.println(this.employeeService.getBirth());
 }
}
```
改善例

```java
public class Employee {
 public int id;
 public String name;
 public LocalDate birth;
}
public class EmployeeService {
 public void create(Employee employee) {
 // employee.id、employee.name、employee.birth の値をファイルに保存する
 }

 public Employee get(int id) {
 // 指定された id と合致するデータをファイルから読み取り、返す
 }
}
public void MainService

```
値を保持する専用のクラスを作成し、そのインスタンスをメソッドに渡すことでライフサイクルを短くし、割り込み処理による値の書き換えを防ぐ


## ライフサイクルを長くして性能を上げる

先程の内容と矛盾をしているが、意図的にライフサイクルを長くすることで性能を上げることができる。ライフサイクルを短くすると、短命なオブジェクトがたくさんでき、それだけガベージコレクション（GC）の発生回数が増加

GCはアプリケーションのパフォーマンスを悪化させる要因の1つでもあるため、回数、時間ともに減らしたほうがパフォーマンスは良くなる。









