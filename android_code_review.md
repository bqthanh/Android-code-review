#Androidコーディング規則

1. 

**アプリケーション名**：UpperCamelCase

**プロジェクト名**：UpperCamelCase

**クラス名**：UpperCamelCase

**パッケージ名**

- すべて小文字
- ファイル・クラス名：UpperCamelCase

**メソッド名**：lowerCamelCase

**プロパティ名・変数名**

- lowerCamelCase
- アンダースコア(_)やドル記号(＄)で初めてはいけない
- publicではなく、staticでもないプロパティ名は、mで始める
- staticなプロパティ名は、sで始める
- メソッド中のprivateな変数には、mを付けない

### 定数名
| 用途     | 接頭辞        | 
| :-------- | :--------     |
| Intentのaction    | ACTION_       |
| Permission    | PERMISSION_       |
| BundleやIntentのフィールド名    | EXTRA_(型名)_ |


### Drawale files
| Assets Type   | Prefix        | Example                |
| :--------     | :--------     | :--------              |
| Action bar    | ab_           |  ab_stacked.png        |
| Button        | btn_          |  btn_ok.png            |
| Dialog        | dialog_       | dialog_confirm.png     |
| Divider       | divider_      | divider_horizontal.png |
| Icon          | ic_           | ic_star.png            |
| Menu          | menu_         | menu_submenu_bg.png    |
| Notification  | notification_ | notification_bg.png    |
| Tabs          | tab_          | tab_pressed.png        |

### Selector states
| State     | Suffix        | Example                 |
| :-------- | :--------     | :--------               |
| Normal    | _normal       |  btn_order_normal.png   |
| Pressed   | _pressed      |  btn_order_pressed.png  |
| Focussed  | _focused      |  btn_order_focused.png  |
| Disabled  | _disabled     |  btn_order_disabled.png |
| Selected  | _selected     |  btn_order_selected.png |

### Layout files
| Component  |  Class name  |  Layout name  |
| :--------  | :--------     | :--------               |
| Activity   | UserProfileActivity |  activity_user_profile.xml  |
| Fragment   | SignUpFragment      |  fragment_sign_up.xml  |
| Dialog     | ChangePasswordDialog      |  dialog_change_password.xml  |
| AdapterView item |   ...   |  item_person.xml |
| Partial layout   | _selected     |  partial_stats_bar.xml |



### String
| Prefix    |  Description  |
| :-------- |  :--------    |
| error_    |  An error message  |
| msg_      |  A regular information message  |
| title_    |  A title, i.e. a dialog title   |
| action_   |  An action such as "Save" or "Create"  |


### Resources naming
|   Element  | Prefix    |
| :--------  | :-------- |
| TextView   |  text_    |
| ImageView  |  image_   |
| Button     |  button_  |
| Menu       |  menu_    |


### フォルダパッケージ構成
controller（直接的にViewを持たない処理）

- provider（通信処理を管理）
- util（その他の処理）

model（POJOやenumを管理）

- pojo（画面名かDBの構成によって更に子フォルダを作成する）
- enumerate（enumを管理）
- system（アプリ自体の情報を保持するクラスを管理するex:AppConfig.java,Constants.java）


view（layoutファイルと関連付けられるもの・viewcontrollerを管理）

- activity
- adapter
- fragment
- widget


・コメントが十分

2. ソースコードロジック
・スーソコードのBlockが十分に小さい
・例外処理が十分
・全てのビジネスを通過出来るロジックを実施する
・Permissionを十分に宣言するし、余裕なPermissionを宣言しない
・最新で安定バージョンのライブラリ、自動ビルドシステムを使用する

3. その他
・スースコード構築が良いか悪いか
・フローデザインと潜在的リスク
・メモリに関する効果的に利用しているか、メモリーリークしないように確認する
