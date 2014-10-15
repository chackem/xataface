#Xataface Delegate Classes

Xataface applications provide quite a number of mechanisms for customizing application behaviour. One of the most fundamental of these is the *Delegate Class*.  A Delegate class is a class that can be implemented to provide customized functionality to your application by implementing methods that follow naming conventions.

There are two types of delegate classes:

1. **Table Delegate Classes** : Override behaviour pertaining to a particular table.
2. **Application Delegate Class** : Override behaviour pertaining to the entire application.

##What Can You Do With A Delegate Class?

1. Customize Permissions
2. Override portions of the UI with custom HTML content. (Slots)
3. Insert custom HTML content into parts of the UI. (Blocks and Sections)
4. Override the representation of field content in various formats. E.g. HTML, CSV, Text, etc..
5. Implement event handlers or "triggers" to execute custom PHP code during specified events.  E.g. *before insert*, *after insert*, *before update*, *after user registration*, *before handle request*, etc...
6. Add custom initialization to tables and fields.
7. Create custom fields.

There are many other things you can do with delegate classes also.  In fact most tutorials that involve customizing application behaviour will involve delegate classes in some form.

<a name="application-delegate-example"></a>

##How to Add an Application Delegate Class to Your App

1. Create a directory named *conf* in your application's root directory.
2. Create a file named *ApplicationDelegate.php* inside your *conf* directory with the following contents:
 
 ~~~
 <?php
 class conf_ApplicationDelegate {
 
 }
 ~~~

3. Verify that your application is picking it up, by implementing a supported method and checking to make sure that it is executed.  In this example, we'll implement the `beforeHandleRequest()` method, which is called before every HTTP request:

 ~~~
 <?php
 class conf_ApplicationDelegate {
   function beforeHandleRequest(){
      echo "In beforeHandleRequest";
      exit;
   }
 }
 ~~~
Now, try loading your app.  You should see:

 ~~~
 In beforeHandleRequest
 ~~~
in your browser if the delegate class is being picked up.  If you don't see this, then either it isn't being picked up at all, or it isn't finding the `beforeHandleRequest` method.  In this case, see the [troubleshooting](#application-delegate-troubleshooting) section for more details.

<a name="table-delegate-example"></a>

##How to Add a Table Delegate Class to Your App

1. Create a directory named *tables* in your application's root directory.
2. Create a directory with the same name as your table inside the *tables* directory.  If the table is named *people*, then this directory will be located at `APP_ROOT/tables/people`.
3. Create a PHP file named after the table inside the directory you just created.  If the table is named *people*, this file will be located at `APP_ROOT/tables/people/people.php`.  It should have the following contents:

 ~~~
 <?php
 class tables_people {
 
 }
 ~~~
4. Verify that your application is picking it up by implementing a supported method and checking to make sure that it is executed.  In this example, we'll implement the `init()` method which is called the first time the table is loaded in a request:

 ~~~
 <?php
 class tables_people {
   function init(Dataface_Table $table){
      echo "In people init()";
      exit;
   }
 }
 ~~~

Now try loading your app in the context of your table.  E.g. If your table name is *people*, the app URL to test would be `index.php?-table=people`.  You should see:

 ~~~
 In people init()
 ~~~
If you don't see this, see the [troubleshooting](#table-delegate-troubleshooting) section.

##Application Delegate Troubleshooting

If your application doesn't seem to be picking up your application delegate class after following the [instructions above](#application-delegate-example), this section describes how to debug the issue.  There are two possible scenarios:

1. There is an error.
2. There is no error, the app just runs normally as if there is no Application delegate defined.

###Resolving Errors

If, after adding an Application Delegate, you either receive an error or a blank white screen in your app, you likely have a syntax error in your delegate class.  If you have a blank white screen, your first order of business is to locate the error log.  **Now is the best time to find your PHP error log!!**  If you don't know where your error log is you need to find it.  Without it, you are flying blind and you will begin to hate life before long.

**Common Mistakes:**

1. **Your class has the wrong name**. The class name must be exactly `conf_ApplicationDelegate`.  If you name this wrong, but your PHP file is named correctly, you will receive errors about "Class Not Found", etc..
2. You simply have a syntax error.  Find out what the error is in your error log, and fix it.
3. Your `<?php` open tag is missing or malformed.

###Resolving "Nothing Happened"

If your application runs normally except that it didn't seem to execute your `echo` statement, then either your PHP file is named incorrectly, it is in the wrong location, or the `beforeHandleRequest` method has been named incorrectly.  Make sure that the PHP file is exactly at `APP_ROOT/conf/ApplicationDelegate.php` (where *APP_ROOT* is just the path to your application's directory).  Then make sure that your `beforeHandleRequest()` method is named exactly that.

If you are certain that the PHP file is named correctly, and located in the correct location, you may want to double-check the permissions on your `conf` directory to make sure that it is readable and navigable by the web server process.

##Table Delegate Troubleshooting

If your application doesn't seem to be picking up a table delegate class after following the [instructions above](#table-delegate-example), this section describes how to debug the issue.  There are two possible scenarios:

1. There is an error.
2. There is no error, the app just runs normally as if there is no table delegate.

###Resolving Errors

If, after adding a table delegate, you either receive an error or a blank white screen in your app, you likely have a syntax error in your delegate class.  If you have a blank white screen, your first order of business is to locate the error log.  **Now is the best time to find your PHP error log!!**  If you don't know where your error log is you need to find it.  Without it, you are flying blind and you will begin to hate life before long.

**Common Mistakes:**

1. **Your class has the wrong name**. The class name must be exactly `tables_TABLENAME` (where *TABLENAME* is the name of the table that the delegate class is for)  E.g. `tables_people` if the table is named "people".  If you name this wrong, but your PHP file is named correctly, you will receive errors about "Class Not Found", etc..
2. You simply have a syntax error.  Find out what the error is in your error log, and fix it.
3. Your `<?php` open tag is missing or malformed.

###Resolving "Nothing Happened"

If your application runs normally except that it didn't seem to execute your `echo` statement, then either your PHP file is named incorrectly, it is in the wrong location, or the `init()` method has been named incorrectly.  Make sure that the PHP file is exactly at `APP_ROOT/tables/TABLENAME/TABLENAME.php` (where *APP_ROOT* is just the path to your application's directory, and *TABLE_NAME* is the name of your table).  E.g. If the table is named *people* it would be located at `APP_ROOT/tables/people/people.php`. **This is case sensitive!**  If your table is named `People`, then the delegate class would be located at `APP_ROOT/tables/People/People.php`.

 Then make sure that your `init()` method is named exactly that.

If you are certain that the PHP file is named correctly, and located in the correct location, you may want to double-check the permissions on your `tables` directory to make sure that it is readable and navigable by the web server process.

##Supported Methods

Delegate classes support many different method interfaces, and they can be expanded to support an infinite number of methods.  E.g. Module developers can create their own protocols for delegate class methods for use in their modules.  In addition, many delegate classes simply follow naming conventions, giving you, as an app developer, to implement unlimited methods that affect the behaviour of your application.

The following is a list of some of the common methods that are supported by delegate classes, but this list is by no means exhaustive

###Triggers

| Name | Description | Table | App |
|---|---|---|---|
| `beforeInsert` | Fired before a record is inserted | Y |  |
| `afterInsert` | Fired after a record is inserted | Y |    |
| `beforeSave` | Fired before a record is saved (inserted or updated) | Y | |
| `afterSave` | Fired after a record is saved (inserted or updated) | Y | |
| `beforeUpdate` | Fired before a record is updated | Y |  |
| `afterUpdate ` | Fired after a record is updated | Y | |
| `beforeHandleRequest` | Fired at the beginning of each request.  This is the most common place to add custom request handling - like modifying the query, etc... |  | Y |
| `after_action_new` | Fired after the `new` action is completed successfully. | Y |  |
| `after_action_edit` | Fired after the `edit` action is completed successfully. | Y | |