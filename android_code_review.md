#Android Coding convention

### 1. Naming convention

| Type           | Style       |
| :------------- | :------------ |
| Application name | UpperCamelCase |
| Project name  | UpperCamelCase |
| Package name  | All lowercase |
| Class name    | UpperCamelCase  |
| Method name   | lowerCamelCase  |
| Variable      | lowerCamelCase<br/> + A property name that is neither public nor static begins with m<br/> + A static property name starts with s|
| Constant  | Use capital letters and underscores only |

### 2. File naming

### 2.1 Class files
Class names are written in UpperCamelCase. 
For classes that extend an Android component, the name of the class should end with the name of the component; for example: SignInActivity, SignInFragment, ImageUploaderService, ChangePasswordDialog.

### 2.2 Drawable files
| Assets Type   | Prefix        | Example        |
| :--------     | :--------     | :--------      |
| Action bar    | ab_           | ab\_stacked.png         |
| Button        | btn_          | btn\_ok.png             |
| Dialog        | dialog_       | dialog\_confirm.png     |
| Divider       | divider_      | divider\_horizontal.png |
| Icon          | ic_           | ic\_star.png            |
| Menu          | menu_         | menu\_submenu\_bg.png   |
| Notification  | notification_ | notification\_bg.png    |
| Tabs          | tab_          | tab\_pressed.png        |

#### Selector states
| State     | Suffix        | Example     |
| :-------- | :--------     | :--------   |
| Normal    | \_normal      |  btn\_order\_normal.png   |
| Pressed   | \_pressed     |  btn\_order\_pressed.png  |
| Focussed  | \_focused     |  btn\_order\_focused.png  |
| Disabled  | \_disabled    |  btn\_order\_disabled.png |
| Selected  | \_selected    |  btn\_order\_selected.png |

### 2.3 Layout files
| Component  |  Class name  |  Layout name  |
| :--------  | :--------    | :--------     |
| Activity   | UserProfileActivity |  activity\_user\_profile.xml  |
| Fragment   | SignUpFragment      |  fragment\_sign\_up.xml  |
| Dialog     | ChangePasswordDialog  |  dialog\_change\_password.xml  |
| AdapterView item | ...   |  item\_person.xml |
| Partial layout   | ...   |  partial\_stats\_bar.xml |

### 3. Resource naming

### ID naming
|   Element  | Prefix    |
| :--------  | :-------- |
| TextView   |  text_    |
| ImageView  |  image_   |
| Button     |  button_  |
| Menu       |  menu_    |

### String
| Prefix    |  Description  |
| :-------- |  :--------    |
| error_    |  An error message  |
| msg_      |  A regular information message  |
| title_    |  A title, i.e. a dialog title   |
| action_   |  An action such as "Save" or "Create"  |


### 3. Package structure

ui

- activity
- fragment
- dialog
- widget
- adapter

controller

- provider
- util

data

- remote
- local
- model
- system: ex:AppConfig.java,Constants.java


#### Source block
- Comments are enough
- Block of the source code is sufficiently small
- Exception handling is sufficient
- Implement logic that can pass through all businesses
- Declare Permission adequately and do not declare affordable Permission
- Use latest and stable version libraries and automatic build system

#### Others
- Whether the source code construction is good or bad
- Flow design and potential risk
- Make sure that you are effectively using memory and do not leak memory
