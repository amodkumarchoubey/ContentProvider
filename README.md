# ContentProvider

Content Provider facilitates access to a central data store or warehouse to allow data sharing and data manipulation across different applications.
Here are a few examples of default Content Providers in Android system’s API:
1.Call Logs 2. Address book.

Content Providers are the only way to share data across Android applications. They store and retrieve data thus making it accessible to all.
Content Providers give a uniform interface to access the data.
Android platform provides default implementations of content providers for data types like audio, video, images, contact information etc.

A content provider component supplies data from one application to others on request. Such requests are handled by the methods of the ContentResolver class. A content provider can use different ways to store its data and the data can be stored in a database, in files, or even over a network. 

ContentProvider is one of the pillars of Android. It is a component which
i)- hides database details (database name, table name, column info. etc.)
ii)- allows the application to share data among multiple applications. 

•	onCreate() This method is called when the provider is started.
•	query() This method receives a request from a client. The result is returned as a Cursor object.
•	insert()This method inserts a new record into the content provider.
•	delete() This method deletes an existing record from the content provider.
•	update() This method updates an existing record from the content provider.
•	getType() This method returns the MIME type of the data at the given URI. 

 

How can an application access the database of other application ?
Android operating system allocates separate process for each app. Every app runs in its own process.
Suppose there are 2 applications say Application 1 and Application 2. Application2 wants to access the DB present in Application 1. Application2 cannot directly access the database of Application1 as the database is private to application1.
Application 2 makes a data request through ContentResolver object. Once application 1 receives the request through ContentResolver then the content provider of application 1 will send the data as response.
Note: Application 2 should have proper permissions to query the content provider of Application 1.
How to access Content provider using Content Resolver?
We need to use a method called getContentResolver() using which application can make data request to application.
In order to hit proper content Provider we need Uniform Resource Identifier (URI) using which application can make data request to application 1. URI is similar to URL.
Content Resolver has certain methods using which we can perform CRUD operations such as Create,Retrieve,Update and Delete operations.
Example of Content Provider:
ContactsContract.Data to access contacts info.
Suppose we have an app that needs to fetch the contacts or modify the contacts,it needs to access contacts content provider , if it have proper permission read and write permissions to access contacts content provider.
How to query a content provider using Content Resolver?
Cursor c = getContentResolver().query(ContentURI, mProjections,SelectionClause,Selectionards, sortOrder)
where
contentURI : ContentProvider’s unique URI.
mProjections : columns to be accessed
selectionClause: where condition (col1 = ? AND col2 = ?)
selection Args : provides values to selection clause at run time in a String Arrray substituting the Question marks (?) above .
sortorder: Order in which the rows should be fetched . order by first name or second name etc.
How to traverse through cursor ?
Code Fragment:
Cursor c = getContentResolver().query(ContentURI, mProjections,SelectionClause,Selectionards, sortOrder);
if(c ! =null && c.getCount>0){
if(c.moveToFirst()){
do{
//Obtain info
while(c.moveToNext());
}
}
}
Permissions to access Content Provider:
Add proper permissions to access content provider.
Suppose your app is going to read contacts from contacts content provider then add proper permission in manifest file
<uses-permission android:name = "android.permission.READ_CONTACTS" />
In Activity:
private String[] columnProjection = new String[]{            ContactsContract.Contacts.DISPLAY_NAME_PRIMARY,            ContactsContract.Contacts.CONTACT_STATUS};
Cursor cursor=contentResolver.query(ContactsContract.Contacts.CONTENT_URI,                mColumnProjection,                null,                null,                null);
Where ContactsContract.contact.CONTENT_URI is uri of contacts content Provider
How to Create Custom Content Provider ?
Steps to Create Custom Content Provider:
What are the situations in which we need Custom Content provider ?
When we want to expose our Database to other apps.
We need to copy complex data from app to other app.
Step 1: Our data base should have primary keys
Step 2: Create a class that extends ContentProvider
Step 3: Create CONTENT_URIs to expose data of content providers.
Creating a content uri contains 3 parts:
CONTENT : content://
Authority : PACKAGE NAME
PATH : user defined String constants.
Example: content://customContentProvider.com.andr/FETCH_ALL_USER_INFO
Step 4:
Example:
Declarations:
public class MyProvider extends ContentProvider {
public static final String AUTHORITY  =   
"customContentProvider.com.andr"
public static final String PATH_FETCH_ALL_INFO = "FETCH_ALL_INFO"
public static final String PATH_FETCH_SPECIFIC_INFO = "FETCH_SPECIFIC_INFO"
public static final String PATH_FETCH_COUNT = "FETCH_COUNT"
 protected void onCreate(){ 
    }
}
Creating Content URI:
public static final URI FETCH_ALL_CONTENT_URI= URI.parse("content://"+AUTHORITY+PATH_FETCH_ALL_INFO)
public static final URI FETCH_SPECIFIC_INFO_URI= URI.parse("content://"+AUTHORITY+PATH_FETCH_SPECIFIC_INFO)
public static final URI FETCH_COUNT_URI= URI.parse("content://"+AUTHORITY+PATH_FETCH_COUNT)
Note: The Above constants will be used by ContentResolver to send the request.
Step 5: Mapping ContentURI received from ContentResolver to respond back with necessary data.
Use URIMatcher to match the ContentURI received from ContentResolver.
URIMatcher:
Matches ContentURIs with another set of Integer Constants.
Step 6: Create new set of Integer Constants to match Content URIs
public static final int FETCH_ALL_DATA_MATCHER  = 1
public static final int FETCH_SPECIFIC_DATA   = 2 
public static final int FETCH_COUNT = 3
Step 7: Create new URIMatcher
URIMatcher matcher = new URIMatcher(URIMatcher.NO_MATCH)
Step 8: Matching PATH(String constants) with Integer Constants
matcher.addURI(AUTHORITY,PATH_FETCH_ALL_INFO,FETCH_ALL_DATA_MATCHER)
matcher.addURI(AUTHORITY,PATH_FETCH_SPECIFIC_INFO,FETCH_SPECIFIC_DATA)
matcher.addURI(AUTHORITY,PATH_FETCH_COUNT,FETCH_COUNT)
Step 9 : Override getType() method to match URI

public String getType(URI uri){
switch(matcher.match(uri)){
case FETCH_ALL_DATA_MATCHER: 
return CONTENT_TYPE_ALL;
break;
case FETCH_SPECIFIC_DATA:
return CONTENT_TYPE_SPECIFIC;
break;
case FETCH_COUNT:
return CONTENT_TYPE_COUNT;
break;
}
}
Where
public static final String CONTENT_TYPE_ALL  = ContentResolver.CURSOR_DIR_BASE_TYPE+"/"+PATH_FETCH_ALL_INFO;
public static final String CONTENT_TYPE_SPECIFIC  = ContentResolver.CURSOR_DIR_BASE_TYPE+"/"+PATH_FETCH_SPECIFIC_INFO;
public static final String CONTENT_TYPE_SPECIFIC  = ContentResolver.CURSOR_ITEM_BASE_TYPE+"/"+PATH_FETCH_COUNT;
Step 6: Implementing the below methods:
override the below methods:
onCreate()
insert()
delete()
update()
getType()
Step 7 :
In onCreate() we need to perform one time initialization process.
In this we need to create instance of DataBase class (DBHelper) that contains implementation of CRUD operations.
Step 7 : Query()
Query() is method is used to query data from Content Provider.
App2 uses contentResolver to query data from content Provider with the proper args.
The first argument of query method contains URI which will be matched with URIMatcher to return the corresponding cursor.
The query method should be implemented as below:
@Nullable
 @Override
 public Cursor query(@NonNull Uri uri, @Nullable String[] cols, @Nullable String where, @Nullable String[] selecArgs, @Nullable String orderBy) {
Cursor cursor=null;
 switch (MATCHER.match(uri)){
 case FETCH_ALL_DATA_MATCHER: cursor=deHelper.fetchCursorsForAllInfo();break;
 case FETCH_SPECIFIC_DATA: cursor=dbAdapter.fetchCursorForSpecificData(selecArgs[0]);break;
 case FETCH_COUNT:cursor=dbAdapter.getCount();break;
 default:cursor=null; break;
 }
 return cursor;
 }
insert()
Basically insert() method is used to insert a row in the DataBase .
The insert method returns Uri whene a row is inserted .
The returned Uri is appended with newly added row Id .
Insert implementation:
@Nullable
    @Override
    public Uri insert(@NonNull Uri uri, @Nullable ContentValues contentValues) throws UnsupportedOperationException{
        Uri finalUri = null;
        switch (MATCHER.match(uri)){
            case FETCH_ALL_DATA_MATCHER: 
            returnUri= insertData(uri,contentValues);break;
            default: new UnsupportedOperationException("insert Failed "); break;
        }
return finalUri;
    }
private Uri insertData(Uri uri, ContentValues cv){  
     long id = dbHelper.insert(cv);        getContext().getContentResolver().notifyChange(uri,null);        return Uri.parse("content://"+AUTHORITY+"/"+ PATH_TODO_LIST +"/"+id);   
 }
delete()
The delete method of content provider returns integer value, that specifies number of rows deleted.
Arguments for delete() include:
Uri,
where condition
selection args .
delete(Uri uri, String where, String[] selectionArgs)
Delete Implementation:
@Override
    public int delete(@NonNull Uri uri, @Nullable String where, @Nullable String[] selecArgs) throws UnsupportedOperationException{
        int deleteRowCount=-1;
        switch (MATCHER.match(uri)){
            case FETCH_ALL_DATA_MATCHER:
 deleteRowCount= dbHelper.delete(where,selecArgs);
break;
            default:new UnsupportedOperationException("Delete Failed"); break;
        }
        return deleteRowCount;
    }
update()
This methods returns the integer , that specifies number of rows that are updated when update() operation is performed.
int update(Uri uri,ContentValues contentValues,String where, String[] selecArgs)
@Override
    public int update(@NonNull Uri uri, @Nullable ContentValues cv, @Nullable String where, @Nullable String[] selectArgs) throws UnsupportedOperationException{
        int updatedRows=-1;
        switch (MATCHER.match(uri)){
            case FETCH_ALL_DATA_MATCHER: updatedRows=dbHelper.update(cv,where,selectArgs);
break;
            default:new UnsupportedOperationException("update Failed..."); break;
        }
        return updatedRows;
    }
getType():
Specifies the type of data that is returned by content provider’s contentUri.Its also called mimeType.
Returns String Type as response when called by getContentResolver().getType(Uri);
The below are some of the return values of getType() method:
ContentResolver.CURSOR_DIR_BASE_TYPE — Specifies the returns all the rows from ContentProvider.
ContentResolver.CUROSR_ITEM_BASE_TYPE — Specifies that the contentUri returns single item information from content Provider.
@Nullable
 @Override
 public String getType(@NonNull Uri uri) {
 switch (MATCHER.match(uri)){
 case FETCH_ALL_DATA_MATCHER: return MIME_TYPE_1;
 case FETCH_SPECIFIC_DATA: return MIME_TYPE_2;
 case FETCH_COUNT: return MIME_TYPE_3;
 }
 return null;
 }
public static final String MIMETYPE1 = ContentResolver.CURSOR_DIR_BASE_TYPE+”/”+ “vnd.com.mypackage.fetchall”;
 public static final String MIMETYPE2 = ContentResolver.CURSOR_DIR_BASE_TYPE+”/”+ “vnd.com.mypackage.fetchSingle”;
 public static final String MIMETYPE3 = ContentResolver.CURSOR_ITEM_BASE_TYPE+”/”+ “vnd.com.mypackage.fetchCount”;
Android recommendations for creating custom MIME Types:
(1) Start with vnd
(2) Add the custom string
(3) append the formed string with Android MIME Type
ContentResolver.CURSOR_DIR_BASE_TYPE+”/”+”vnd.com.myPackage.fetchall”
Finally:
Create a DBHelper class of your own with all CRUD operations implemented in order to access them from custom Content Provider.
Create a client App (App2) with User interface that can access the DataBase and perform CRUD operations through contenProvider using Content Resolver.
Make sure to expose custom content provider to public using android:exported = true in APP1’s manifest file.
