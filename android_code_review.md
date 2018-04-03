# Android Code Reviews
 

## I. Coding conventions

### 1. Project guidelines 

### 1.1 Package structure

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


### 1.2 File naming

#### 1.2.1 Class files

Class names are written in [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase).

For classes that extend an Android component, the name of the class should end with the name of the component. For example: `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog`.

#### 1.2.2 Resources files

Resources file names are written in __lowercase_underscore__.

#### 1.2.2.1 Drawable files

Naming conventions for drawables:

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


Naming conventions for icons:

| Asset Type                      | Prefix             | Example                      |
| --------------------------------| ----------------   | ---------------------------- |
| Icons                           | `ic_`              | `ic_star.png`                |
| Launcher icons                  | `ic_launcher`      | `ic_launcher_calendar.png`   |
| Menu icons and Action Bar icons | `ic_menu`          | `ic_menu_archive.png`        |
| Status bar icons                | `ic_stat_notify`   | `ic_stat_notify_msg.png`     |
| Tab icons                       | `ic_tab`           | `ic_tab_recent.png`          |
| Dialog icons                    | `ic_dialog`        | `ic_dialog_info.png`         |


Naming conventions for selector states:

| State	       | Suffix          | Example                     |
|--------------|-----------------|-----------------------------|
| Normal       | `_normal`       | `btn_order_normal.9.png`    |
| Pressed      | `_pressed`      | `btn_order_pressed.9.png`   |
| Focused      | `_focused`      | `btn_order_focused.9.png`   |
| Disabled     | `_disabled`     | `btn_order_disabled.9.png`  |
| Selected     | `_selected`     | `btn_order_selected.9.png`  |

#### 1.2.2.2 Layout files

| Component        | Class Name             | Layout Name                   |
| ---------------- | ---------------------- | ----------------------------- |
| Activity         | `UserProfileActivity`  | `activity_user_profile.xml`   |
| Fragment         | `SignUpFragment`       | `fragment_sign_up.xml`        |
| Dialog           | `ChangePasswordDialog` | `dialog_change_password.xml`  |
| AdapterView item | ---                    | `item_person.xml`             |
| Partial layout   | ---                    | `partial_stats_bar.xml`       |


#### 1.2.2.3 Menu files
Similar to layout files, menu files should match the name of the component. For example, if we are defining a menu file that is going to be used in the `UserActivity`, then the name of the file should be `activity_user.xml`


## 2. Code guidelines

### 2.1 Javadoc

Javadoc only requires a '/\*\*' at the start and a '*/' at the end. In addition to this, use a single star on each additional line:

``` java
/**
 * Multiple lines of Javadoc text are written here,
 * wrapped normally...
 */
public int method(String p1) { ... }
```
... or in this single-line example:

``` java
/** An especially short bit of Javadoc. */
```

### 2.2 Exception handling

#### 2.2.1 Clean up Resources in a Finally Block or Use a Try-With-Resource Statement.

A common mistake in these situations is to close the resource at the end of the try block.

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
You will not close the resources when an exception throw.

You should, therefore, put all your clean up code into the finally block or use a try-with-resource statement.

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

**Java 7’s Try-With-Resource Statement**

You can use it if your resource implements the AutoCloseable interface. That’s what most Java standard resources do. When you open the resource in the try clause, it will get automatically closed after the try block got executed, or an exception handled.

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

#### 2.2.2 Prefer Specific Exceptions

Always try to find the class that fits best to your exceptional event, e.g. throw a NumberFormatException instead of an IllegalArgumentException. And avoid throwing an unspecific Exception.

``` java
public void doNotDoThis() throws Exception { ... }
	
public void doThis() throws NumberFormatException { ... }
```

#### 2.2.3 Document the Exceptions You Specify

``` java
/**
* This method does something extremely useful ...
*
* @param input
* @throws MyBusinessException if ... happens
*/
public void doSomething(String input) throws MyBusinessException { ... }
```

#### 2.2.4 Throw Exceptions With Messages Messages

If you throw a specific exception it's class name will most likely already describe the kind of error. There for it's message only needs to provide the input string that caused the problem.

``` java
try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
}
```

#### 2.2.5 Don't catch Throwable

Throwable is the superclass of all exceptions and errors. You can use it in a catch clause, but you should never do it!

``` java
public void doNotCatchThrowable() {
	try {
		// do something
	} catch (Throwable t) {
		// don't do this!
	}
}
```

#### 2.2.6 Don’t ignore exceptions

``` java
public void doNotIgnoreExceptions() {
	try {
		// do something
	} catch (NumberFormatException e) {
		// this will never happen
	}
}
```

Please, never ignore an exception. You don’t know how the code will change in the future. Someone might remove the validation that prevented the exceptional event without recognizing that this creates a problem.

You should at least write a log message telling everyone that the unthinkable just had happened and that someone needs to check it.


``` java
public void logAnException() {
	try {
		// do something
	} catch (NumberFormatException e) {
		log.error("This should never happen: " + e);
	}
}
```

#### 2.2.7 Don’t log and throw

The below example will write multiple error messages for the same exception.

``` java
try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
	throw e;
}
```

If you need to add additional information, you should catch the exception and wrap it in a custom one.

``` java
public void wrapException(String input) throws MyBusinessException {
	try {
		// do something
	} catch (NumberFormatException e) {
		throw new MyBusinessException("A message that describes the error.", e);
	}
}
```

### 2.3 Java style rules

#### 2.3.1 Fields definition and naming

Fields should be defined at the __top of the file__ and they should follow the naming rules listed below.

* Non-public, non-static field names start with __m__.
* Private, static field names start with __s__.
* Other fields start with a lower case letter.
* Static final fields (constants) are ALL\_CAPS\_WITH\_UNDERSCORES.

Example:

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

### 2.3 XML style rules

#### 2.3.1 Use self closing tags

When an XML element doesn't have any contents, you __must__ use self closing tags.

This is good:

```xml
<TextView
	android:id="@+id/text_view_profile"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

This is __bad__ :

```xml
<!-- Don\'t do this! -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```

### 2.3.2 Resources naming

Resource IDs and names are written in __lowercase_underscore__.

#### 2.3.2.1 ID naming

IDs should be prefixed with the name of the element in lowercase underscore. For example:


| Element            | Prefix            |
| -----------------  | ----------------- |
| `TextView`         | `text_`           |
| `ImageView`        | `image_`          |
| `Button`           | `button_`         |
| `Menu`             | `menu_`           |

Image view example:

```xml
<ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

Menu example:

```xml
<menu>
	<item
        android:id="@+id/menu_done"
        android:title="Done" />
</menu>
```

#### 2.3.2.2 Strings

String names start with a prefix that identifies the section they belong to. For example `registration_email_hint` or `registration_name_hint`. If a string __doesn't belong__ to any section, then you should follow the rules below:


| Prefix             | Description                           |
| -----------------  | --------------------------------------|
| `error_`           | An error message                      |
| `msg_`             | A regular information message         |
| `title_`           | A title, i.e. a dialog title          |
| `action_`          | An action such as "Save" or "Create"  |


#### 2.3.2.3 Styles and Themes

Unlike the rest of resources, style names are written in __UpperCamelCase__.


## II. Code review critials

#### 1. Coding conventions

Do code follow coding conventions ?

#### 2. Logic errors

It doesn’t matter how experienced you are, you can always have logic errors. That’s why it is good to understand and check all the conditions of the code that you are reading.

#### 3. Null Pointer Exceptions

One of the most common types of crashes that are in the wild while you develop are caused by trying to run methods on objects that are not instantiated.

#### 4. Code Reuse

The truth is that by reusing code we contribute to keep the app’s code simpler and avoid having new crashes on production.

![](https://cdn-images-1.medium.com/max/1600/1*-nhSONLZxx0wcOsr20KULw.png)

#### 5. Save the App's State

One of the things we tend to forget is saving the app’s state and when that happens (after switching between apps, getting the app killed or rotating the phone) we loose the state of what the user was doing. So, this is something to take into account when reviewing code.

#### 6. Simplified Layout

One of the trickiest part of Android development is to keep simplified layouts and stick to what the design team have designed. It is important to pay attention to the amount of views and nested views used to keep the layout simple without compromising what was designed.

![](https://cdn-images-1.medium.com/max/1600/1*6p6Q1lY1xH3rNdEvKKWKUA.png)

#### 6. Dimens, Strings and Constants

Once you have unified all your dimens, strings and constants on each file you will find out how easy is to modify values and behaviors of the app.

It is important to avoid hardcoding values on every file of the app to keep simplicity on code and happiness of the other fellow developers.

#### 7. Localization

Although it is obvious, not everyone speaks the same language around the globe.

Next time you check some pull requests take into account the languages that your app supports and make sure you are providing accurate translations for each of them.




- Exception handling is sufficient
- Block of the source code is sufficiently small
- Implement logic that can pass through all businesses
- Declare Permission adequately and do not declare affordable Permission
- Use latest and stable version libraries and automatic build system

- Whether the source code construction is good or bad
- Flow design and potential risk
- Make sure that you are effectively using memory and do not leak memory