---
title: Implementing Sign-in with Microsoft on an Android application - Setup
description: How to  implement demonstrates how to implement Sign-In with Microsoft on a native Android application using the OpenID Connect standard | Microsoft Azure
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: ''

ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date:
ms.author: andret

---


This section provides step-by-step instructions for how to create a new project to demonstrate how to integrate a native Android application with sign-in with Microsoft so it can query Web APIs that require a token.

| | |
|---------|---------|
|[![Download](../../media/active-directory-mobileanddesktopapp-android-setup/download.png)](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/complete.zip)|Prefer to download this sample's Android Studio project instead? [Download a project](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/complete.zip) and skip to the [Configuration](active-directory-mobileanddesktopapp-android-configure.md) step to configure the code sample before executing.|


# Create a new project 
1.	Open Android Studio, go to: `File` > `New` > `New Project`
2.	Name your Application and click `Next`
3.	Make sure to select *API 21 or newer (Android 5.0)* and click `Next`
4.	Leave `Empty Activity`, click `Next`, then `Finish`


# Install Microsoft Authentication Library (MSAL)
1.	In Android Studio, go to: `Gradle Scripts` > `build.gradle (Module: app)`
2.	Copy and paste the following code under `Dependencies`:

```ruby  
compile 'com.microsoft.identity.client:msal:0.1.0'
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### About this package: 

The package above installs the Microsoft Authentication Library (MSAL). MSAL handles acquiring, caching and refreshing user tokens used to access APIs protected by Azure Active Directory v2 endpoint.
<!--end-collapse-->

# Create your application’s UI

1.	Open: `activity_main.xml` under `res` > `layout`
2.	Change the activity layout from `android.support.constraint.ConstraintLayout` to `LinearLayout`
3.	Copy and paste the following code into the `LinearLayout` node:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:id="@+id/welcome"
    android:visibility="invisible"
    tools:layout_editor_absoluteY="0dp"
    tools:layout_editor_absoluteX="0dp" />

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="88dp"
    android:layout_height="wrap_content"
    android:textAllCaps="false"
    tools:layout_editor_absoluteY="0dp"
    tools:layout_editor_absoluteX="8dp" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:id="@+id/graphData"
    android:visibility="invisible"
    tools:layout_editor_absoluteY="0dp"
    tools:layout_editor_absoluteX="13dp" />

<LinearLayout
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical"
    tools:layout_editor_absoluteY="0dp"
    tools:layout_editor_absoluteX="8dp">

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

### What is Next

[Use](active-directory-mobileanddesktopapp-android-use.md)
