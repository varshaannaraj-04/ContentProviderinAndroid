# Ex.No:3 Create Your Own Content Providers to get Contacts details.

## AIM:

To create your own content providers to get contacts details using Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min. required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as “ex.no.3″ and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Get contacts details and Display details give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the text create your own content providers to get contacts details.
Developed by: VARSHA A
Registeration Number : 212223220121
*/
```
## Main activity java
```
package com.example.contact;

import android.Manifest;
import android.content.ContentProviderOperation;
import android.content.ContentResolver;
import android.content.OperationApplicationException;
import android.content.pm.PackageManager;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.os.RemoteException;
import android.provider.ContactsContract;
import android.util.Log;
import android.view.View;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity
{

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
    }
    public void btnGetContactPressed(View v)
    {
        getPhoneContacts();
    }

    public void btnAddContactPressed(View v)
    {
        addContact("John Doe", "1234567890");
    }

    public void addContact(String name, String phone)
    {
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_CONTACTS) != PackageManager.PERMISSION_GRANTED)
        {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.WRITE_CONTACTS}, 2);
            return;
        }

        ArrayList<ContentProviderOperation> ops = new ArrayList<>();

        ops.add(ContentProviderOperation.newInsert(ContactsContract.RawContacts.CONTENT_URI)
                .withValue(ContactsContract.RawContacts.ACCOUNT_TYPE, null)
                .withValue(ContactsContract.RawContacts.ACCOUNT_NAME, null)
                .build());

        ops.add(ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
                .withValueBackReference(ContactsContract.Data.RAW_CONTACT_ID, 0)
                .withValue(ContactsContract.Data.MIMETYPE, ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE)
                .withValue(ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME, name)
                .build());

        ops.add(ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
                .withValueBackReference(ContactsContract.Data.RAW_CONTACT_ID, 0)
                .withValue(ContactsContract.Data.MIMETYPE, ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE)
                .withValue(ContactsContract.CommonDataKinds.Phone.NUMBER, phone)
                .withValue(ContactsContract.CommonDataKinds.Phone.TYPE, ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE)
                .build());

        try
        {
            getContentResolver().applyBatch(ContactsContract.AUTHORITY, ops);
            Toast.makeText(this, "Contact Added: " + name, Toast.LENGTH_SHORT).show();
            Log.i("CONTACT_PROVIDER_DEMO", "Successfully added contact: " + name);
        }
        catch (RemoteException | OperationApplicationException e)
        {
            Log.e("CONTACT_PROVIDER_DEMO", "Error adding contact: " + e.getMessage());
            Toast.makeText(this, "Error adding contact", Toast.LENGTH_SHORT).show();
        }
    }

    public void getPhoneContacts()
    {
        if(ContextCompat.checkSelfPermission(this , Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED)
        {
            ActivityCompat.requestPermissions(this , new String[] {Manifest.permission.READ_CONTACTS} , 1);
            return;
        }

        ContentResolver contentResolver = getContentResolver();
        Uri uri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI;
        try (Cursor cursor = contentResolver.query(uri, null, null, null, null))
        {
            if (cursor != null && cursor.getCount() > 0)
            {
                Log.i("CONTACT_PROVIDER_DEMO",
                        "TOTAL # of Contacts ::: " + cursor.getCount());

                while (cursor.moveToNext())
                {
                    String contactName = cursor.getString(
                            cursor.getColumnIndexOrThrow(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME));
                    String contactNumber = cursor.getString(
                            cursor.getColumnIndexOrThrow(ContactsContract.CommonDataKinds.Phone.NUMBER));

                    Log.i("CONTACT_PROVIDER_DEMO",
                            "Contact Name ::: " + contactName + " Contact Number ::: " + contactNumber);
                }
            }
            else
            {
                Log.e("CONTACT_PROVIDER_DEMO", "No contacts found or cursor is null");
            }
        }
    }
}
```

## activity_xml
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/white"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="153dp"
        android:layout_height="65dp"
        android:onClick="btnGetContactPressed"
        android:text="Get Contacts"
        android:textColorLink="#000000"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.459" />

    <Button
        android:id="@+id/btnAddContact"
        android:layout_width="153dp"
        android:layout_height="65dp"
        android:layout_marginTop="16dp"
        android:onClick="btnAddContactPressed"
        android:text="Add Contact"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="173dp"
        android:layout_height="31dp"
        android:fontFamily="sans-serif-medium"
        android:text="ContentProvider"
        android:textColor="#6851A5"
        android:textSize="24sp"
        android:typeface="serif"
        app:layout_constraintBottom_toTopOf="@+id/button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.779" />
</androidx.constraintlayout.widget.ConstraintLayout>
```
## Android Manifest
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <uses-permission android:name="android.permission.READ_CONTACTS" />
    <uses-permission android:name="android.permission.WRITE_CONTACTS" />
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Contact">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
## OUTPUT
<img width="1920" height="1080" alt="muid ex4 get contact" src="https://github.com/user-attachments/assets/2679111a-79c6-4799-bf5c-7575278fd2da" />

<img width="1920" height="1080" alt="muid ex4 contact" src="https://github.com/user-attachments/assets/0c598773-7094-40a6-be18-0d95e406e598" />

<img width="1920" height="1080" alt="muid ex4 logcat" src="https://github.com/user-attachments/assets/c1915042-8dd5-4133-bb09-3a196ed78053" />


## RESULT
Thus a Simple Android Application create your own content providers to get contacts details using Android Studio is developed and executed successfully.
