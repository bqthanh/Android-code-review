# Androidコードレビュー
 

## I. コーディング規則

### 1. プロジェクトのガイドライン

### 1.1 パッケージの構造

私はあなたが以下のパッケージ構造をすべきだと思います。

ui

- base
- activity
- fragment
- dialog
- widget
- adapter
- animation

controller

- provider
- util

data

- remote / network
- local
- model
- system: ex:AppConfig.java,Constants.java

service

receiver

App.java


### 1.2 ファイルの命名

#### 1.2.1 クラスファイル

クラス名は[UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase)に記載されています。

Androidコンポーネントを拡張するクラスの場合、クラスの名前はコンポーネントの名前で終わる必要があります。たとえば、「SignInActivity」、「SignInFragment」、「ImageUploaderService」、「ChangePasswordDialog」などです。

#### 1.2.2 リソースファイル

リソースファイル名は__lowercase_underscore__に書き込まれます。

#### 1.2.2.1 描画可能なファイル

描画可能の命名規則：

| Asset Type   | Prefix            |		Example                 |
|--------------| ------------------|--------------------------- |
| Action bar   | `ab_`             | `ab_stacked.9.png`         |
| Button       | `btn_`            | `btn_send_pressed.9.png`   |
| Dialog       | `dialog_`         | `dialog_top.9.png`         |
| Divider      | `divider_`        | `divider_horizontal.9.png` |
| Icon         | `ic_`             | `ic_star.png`              |
| Menu         | `menu_	`           | `menu_submenu_bg.9.png`    |
| Notification | `notification_`   | `notification_bg.9.png`    |
| Tabs         | `tab_`            | `tab_pressed.9.png`        |


アイコンの命名規則：

| Asset Type                      | Prefix             | Example                      |
| --------------------------------| ----------------   | ---------------------------- |
| Icons                           | `ic_`              | `ic_star.png`                |
| Launcher icons                  | `ic_launcher`      | `ic_launcher_calendar.png`   |
| Menu icons and Action Bar icons | `ic_menu`          | `ic_menu_archive.png`        |
| Status bar icons                | `ic_stat_notify`   | `ic_stat_notify_msg.png`     |
| Tab icons                       | `ic_tab`           | `ic_tab_recent.png`          |
| Dialog icons                    | `ic_dialog`        | `ic_dialog_info.png`         |


セレクタ状態の命名規則：

| State	     | Suffix          | Example                     |
|--------------|-----------------|-----------------------------|
| Normal       | `_normal`       | `btn_order_normal.9.png`    |
| Pressed      | `_pressed`      | `btn_order_pressed.9.png`   |
| Focused      | `_focused`      | `btn_order_focused.9.png`   |
| Disabled     | `_disabled`     | `btn_order_disabled.9.png`  |
| Selected     | `_selected`     | `btn_order_selected.9.png`  |


#### 1.2.2.2 レイアウトファイル

| Component        | Class Name             | Layout Name                   |
| ---------------- | ---------------------- | ----------------------------- |
| Activity         | `UserProfileActivity`  | `activity_user_profile.xml`   |
| Fragment         | `SignUpFragment`       | `fragment_sign_up.xml`        |
| Dialog           | `ChangePasswordDialog` | `dialog_change_password.xml`  |
| AdapterView item | ---                    | `item_person.xml`             |
| Partial layout   | ---                    | `partial_stats_bar.xml`       |


#### 1.2.2.3 メニューファイル
レイアウトファイルと同様に、メニューファイルはコンポーネントの名前と一致する必要があります。例えば、`UserActivity`で使用されるメニューファイルを定義する場合、ファイルの名前は`activity_user.xml`でなければなりません

### 2. コードガイドライン

#### 2.1 Javadoc

Javadocは、最初に'/\*\*'を、最後に'*/'を必要とします。 これに加えて、追加の行ごとに1つの星を使用します。

``` java
/**
 * Multiple lines of Javadoc text are written here,
 * wrapped normally...
 */
public int method(String p1) { ... }
```
... またはこの単一行の例では：

``` java
/** An especially short bit of Javadoc. */
```

#### 2.2 例外処理

##### 2.2.1 最後のブロックでリソースをクリーンアップするか、Try-With-Resourceステートメントを使用します。

このような状況でよくある間違いは、tryブロックの最後でリソースを閉じることです。

``` java
public void doNotCloseResourceInTry() {
	FileInputStream inputStream = null;
	try {
		File file = new File("./tmp.txt");
		inputStream = new FileInputStream(file);
		
		// use the inputStream to read a file
		
		// do NOT do this
		inputStream.close();
	} catch (FileNotFoundException e) {
		log.error(e);
	} catch (IOException e) {
		log.error(e);
	}
}
```

例外がスローされたときにリソースを閉じることはありません。

したがって、すべてのクリーンアップコードをfinallyブロックに入れるか、try-with-resourceステートメントを使用する必要があります。

**Use a Finally Block**

``` java
public void closeResourceInFinally() {
	FileInputStream inputStream = null;
	try {
		File file = new File("./tmp.txt");
		inputStream = new FileInputStream(file);
		
		// use the inputStream to read a file
		
	} catch (FileNotFoundException e) {
		log.error(e);
	} finally {
		if (inputStream != null) {
			try {
				inputStream.close();
			} catch (IOException e) {
				log.error(e);
			}
		}
	}
}
```

** Java 7のTry-With-Resourceステートメント**

リソースがAutoCloseableインターフェイスを実装している場合に使用できます。これが、ほとんどのJava標準リソースが行うことです。 try節でリソースを開くと、tryブロックが実行された後、または例外が処理された後で自動的にリソースが閉じられます。

``` java
public void automaticallyCloseResource() {
	File file = new File("./resource_file.txt");
	try (FileInputStream inputStream = new FileInputStream(file);) {
		// use the inputStream to read a file
		
	} catch (FileNotFoundException e) {
		log.error(e);
	} catch (IOException e) {
		log.error(e);
	}
}
```

##### 2.2.2 特定の例外を優先する

例外的なイベントに最適なクラスを常に見つけよう。 IllegalArgumentExceptionの代わりにNumberFormatExceptionをスローします。また、不特定の例外を投げるのを避けてください。

``` java
public void doNotDoThis() throws Exception { ... }
	
public void doThis() throws NumberFormatException { ... }
```

##### 2.2.3 指定した例外の文書化

``` java
/**
* This method does something extremely useful ...
*
* @param input
* @throws MyBusinessException if ... happens
*/
public void doSomething(String input) throws MyBusinessException { ... }
```

##### 2.2.4 メッセージで例外をスローする

特定の例外をスローした場合、そのクラス名はすでにエラーの種類を記述している可能性が高くなります。メッセージは、問題の原因となった入力文字列を提供するだけです。

``` java
try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
}
```

##### 2.2.5 Throwableをキャッチしない

Throwableはすべての例外とエラーのスーパークラスです。 catch節で使用することはできますが、決して実行しないでください！
``` java
public void doNotCatchThrowable() {
	try {
		// do something
	} catch (Throwable t) {
		// don't do this!
	}
}
```

##### 2.2.6 例外を無視しない

``` java
public void doNotIgnoreExceptions() {
	try {
		// do something
	} catch (NumberFormatException e) {
		// this will never happen
	}
}
```

例外を無視しないでください。コードが将来どのように変化するかはわかりません。誰かが例外的な出来事を妨げる妥当性検査を取り除いて、これが問題を引き起こすことを認識しなくてもよい。

考えられないことが起こったばかりで誰かがそれを確認する必要があることを誰にも伝えるログメッセージを少なくとも書き込むべきです。


``` java
public void logAnException() {
	try {
		// do something
	} catch (NumberFormatException e) {
		log.error("This should never happen: " + e);
	}
}
```

##### 2.2.7 ログとスローを行わない

以下の例では、同じ例外に対して複数のエラーメッセージが書き込まれます。

``` java
try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
	throw e;
}
```

追加情報を追加する必要がある場合は、例外をキャッチしてカスタム例外にラップする必要があります。

``` java
public void wrapException(String input) throws MyBusinessException {
	try {
		// do something
	} catch (NumberFormatException e) {
		throw new MyBusinessException("A message that describes the error.", e);
	}
}
```

##### 2.2.8 ヌルポインタの例外

開発中に発生する最も一般的なタイプのクラッシュの1つは、インスタンス化されていないオブジェクトでメソッドを実行しようとしたために発生します。

### 2.3 Javaスタイルのルール

#### 2.3.1 フィールドの定義と命名

フィールドはfile__の__topで定義する必要があります。これらのフィールドは、次の命名規則に従ってください。

* 非公開、非静的フィールド名は__m__で始まります。
* 静的フィールド名は__s__で始まります。
* その他のフィールドは小文字で始まります。
* public static finalフィールド（定数）はALL\_CAPS\_WITH\_UNDERSCORESです。

例：

```java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}
```

### 2.4 XMLスタイルルール

#### 2.4.1 自己終了タグを使用する

XML要素に内容がない場合は、self closingタグを使用する必要があります。

これは__いい__：

```xml
<TextView
	android:id="@+id/text_view_profile"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

これは__悪い__：
```xml
<!-- Don\'t do this! -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```

#### 2.4.2 リソースの命名

リソースIDと名前は、__lowercase_underscore__に書き込まれます。

#### 2.4.2.1 ID命名

IDの先頭には、小文字のアンダースコアの要素の名前を付ける必要があります。 例えば：

| Element            | Prefix            |
| -----------------  | ----------------- |
| `TextView`         | `text_`           |
| `ImageView`        | `image_`          |
| `Button`           | `button_`         |
| `Menu`             | `menu_`           |

画像ビューの例：

```xml
<ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

メニューの例：

```xml
<menu>
	<item
        android:id="@+id/menu_done"
        android:title="Done" />
</menu>
```

#### 2.4.2.2 文字列

文字列名は、それが属するセクションを識別する接頭辞で始まります。 たとえば、`registration_email_hint`や` registration_name_hint`などです。 文字列がどのセクションにも属していない場合は、以下の規則に従ってください：

| Prefix             | Description                           |
| -----------------  | --------------------------------------|
| `error_`           | An error message                      |
| `msg_`             | A regular information message         |
| `title_`           | A title, i.e. a dialog title          |
| `action_`          | An action such as "Save" or "Create"  |


#### 2.4.2.3 スタイルとテーマ

残りのリソースとは異なり、スタイル名は__UpperCamelCase__に書き込まれます。

## II. コードレビューの批判

#### 1. コーディング規則

- コードはコーディング規約に従う
- クラスとメソッドをできるだけ小さくする

#### 2. よく設計された

Android開発の最も難しい部分の1つは、レイアウトを単純化し、設計チームが設計したものに固執することです。設計されたものを妥協することなく、レイアウトを簡単に保つのに使用されるビューと入れ子のビューの量に注意することが重要です。

![](https://cdn-images-1.medium.com/max/1600/1*6p6Q1lY1xH3rNdEvKKWKUA.png)

#### 3. コードの再利用

真実は、コードを再利用することによって、我々はアプリケーションのコードをよりシンプルに保ち、生産時に新たなクラッシュを避けることに貢献するということです。

![](https://cdn-images-1.medium.com/max/1600/1*-nhSONLZxx0wcOsr20KULw.png)

#### 4. 可能なすべての例外をキャッチ

#### 5. 論理エラー

あなたがどのように経験しているかは関係ありません、あなたはいつも論理エラーを持つことができます。だからこそ、あなたが読んでいるコードのすべての条件を理解してチェックすることは良いことです。

#### 6. ディメンション、文字列と定数

各ファイルのすべての次元、文字列、定数を統一したら、アプリケーションの値や振る舞いを簡単に変更できることがわかります。

他の同僚の開発者のコ​​ードと幸福を簡単に保つために、アプリケーションのすべてのファイルに値をハードコードしないようにすることが重要です。

#### 7. ローカリゼーション

明らかですが、誰もが世界中で同じ言語を話すわけではありません。

次にプルリクエストを確認するときは、アプリがサポートしている言語を考慮して、それぞれに正確な翻訳を提供していることを確認してください。

#### 8. アプリケーションの状態を保存する

私たちが忘れる傾向のあるものの1つは、アプリの状態を保存することです。アプリが切り替わった後、アプリが終了するか、電話機を回転させた後に、ユーザーの状況が緩やかになります。したがって、これはコードをレビューする際に考慮する必要があります。

#### 9. メモリリークを回避する

**リークメモリ**これは、それを参照してGCがそれを収集するのを妨げるという意味です。

> **メモリプロファイラ**は、Android Profilerのコンポーネントで、メモリリークやメモリチャーンを特定するのに役立ち、吃音、フリーズ、さらにはアプリケーションのクラッシュにつながります。

##### 9.1 コンテキストアクティビティへの長期間の参照を保持しない

バックグラウンドタスクでは、メモリリークの原因となるため、特定のオブジェクトの参照を保持しないでください。

静的ビューは常に使用可能なので使用しないでください。静的ビューは決して殺されません。

> **注：**アクティビティへの参照は、アクティビティ自体と同じライフサイクルを持つ必要があります。

##### 9.2 コンテキストアクティビティの代わりにコンテキストアプリケーションを使用してみてください

##### 9.3 寿命の後にリスナーを解除することを忘れないでください

onPause / onStop / onDestroyメソッドでリスナーの登録を忘れることを忘れないでください。登録を解除しないことにより、常にアクティビティを生かしておき、リスナーを待っています。

##### 9.4 アクティビティ内の非静的な内部クラスを避ける

ライフサイクルを制御しない場合は、静的な内部クラスを使用し、内部のアクティビティを弱く参照します。この問題の解決策は、ViewRootとその内部クラスであるように、外部クラスへのWeakReferenceを持つ静的内部クラスを使用することです

##### 9.5 ガベージコレクタはメモリリークに対する保険ではありません

##### 9.6 コレクションのビューを使用する

クリアなメモリパターンを持たないコレクションにはビューを配置しないでください。たとえば、WeakHashMapを使用してビューを値として格納します。 WeakHashMapはビューをハードリファレンスとして保存するので、ビューを使用しないほうがよいでしょう。

#### 10. 不要なアクセス権を要求しないようにする

#### 11. ビルドの依存関係を追加する

最新かつ安定したバージョンライブラリと自動ビルドシステムの使用

#### 12. Lintでコードを改善する