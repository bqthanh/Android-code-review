# Android Coding Convention

### 1. Naming convention

| Type           | Style       |
| :------------- | :------------ |
| Application name | UpperCamelCase |
| Project name  | UpperCamelCase |
| Package name  | All lowercase |
| Class name    | UpperCamelCase  |
| Method name   | lowerCamelCase  |
| Variable      | lowerCamelCase<br/> + Non-public, non-static field names start with m<br/> + Static field names start with s|
| Constant  | Use capital letters and underscores only |

### 1.2 File naming

### 1.2.1 Class files
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

### 1.2.3 Layout files
| Component  |  Class name  |  Layout name  |
| :--------  | :--------    | :--------     |
| Activity   | UserProfileActivity |  activity\_user\_profile.xml  |
| Fragment   | SignUpFragment      |  fragment\_sign\_up.xml  |
| Dialog     | ChangePasswordDialog  |  dialog\_change\_password.xml  |
| AdapterView item | ...   |  item\_person.xml |
| Partial layout   | ...   |  partial\_stats\_bar.xml |

### 1.3 Resource naming

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


### 1.4 Package structure

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



### 1.5 Use the standard style for the Javadoc comment

Javadoc only requires a '/\*\*' at the start and a '*/' at the end. In addition to this, use a single star on each additional line:

``` java
 /**
   * Standard comment.
   */
  public ...

  /** Compressed comment. */
  public ...
```



### 2. Source code




#### 2.1 Exception handling

#### 2.1.1 Clean up Resources in a Finally Block or Use a Try-With-Resource Statement.

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

#### 2.1.2 Prefer Specific Exceptions

Always try to find the class that fits best to your exceptional event, e.g. throw a NumberFormatException instead of an IllegalArgumentException. And avoid throwing an unspecific Exception.

``` java
public void doNotDoThis() throws Exception { ... }
	
public void doThis() throws NumberFormatException { ... }
```


#### 2.1.3 Document the Exceptions You Specify

``` java
/**
* This method does something extremely useful ...
*
* @param input
* @throws MyBusinessException if ... happens
*/
public void doSomething(String input) throws MyBusinessException { ... }
```

#### 2.1.4 Throw Exceptions With Messages Messages

If you throw a specific exception it's class name will most likely already describe the kind of error. There for it's message only needs to provide the input string that caused the problem.

``` java
try {
	new Long("xyz");
} catch (NumberFormatException e) {
	log.error(e);
}
```

#### 2.1.5 Don't catch Throwable
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

#### 2.1.6 Don’t ignore exceptions
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

#### 2.1.5 Don’t log and throw

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

**Exception handling is sufficient**




#### Others

- Block of the source code is sufficiently small
- Implement logic that can pass through all businesses
- Declare Permission adequately and do not declare affordable Permission
- Use latest and stable version libraries and automatic build system

#### Others
- Whether the source code construction is good or bad
- Flow design and potential risk
- Make sure that you are effectively using memory and do not leak memory
