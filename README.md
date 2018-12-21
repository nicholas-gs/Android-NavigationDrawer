# Android-Navigation Drawer
_An example illustrating a Navigation Drawer in Android, which is an user interface panel that shows your app's main navigation menu._

A Navigation Drawer is an excellent way to displaying your app's navigation options, as well as other options, to the user. Usually hidden when not in use, it saves valuable screen space and declutters your app's user interface.

This article will show you how to do the following:
- Implement a simple Navigation Drawer as per standard [Navigation Drawer Design](https://developer.android.com/training/implementing-navigation/nav-drawer)
- Handle navigation click events to navigate to other fragments
- Add custom [switch button](https://developer.android.com/reference/android/support/v7/widget/SwitchCompat) to the Navigation Drawer and handle the click events
        
<br>
<img src="https://img.shields.io/badge/minSdkVersion-21-red.svg?style=true" alt="minSdkVersion 21" data-canonical-src="https://img.shields.io/badge/minSdkVersion-24-red.svg?style=true" style="max-width:100%;">
<img src=https://img.shields.io/badge/compileSdkVersion-28-brightgreen.svg alt="compileSdkVersion 28" data-canonical-src="https://img.shields.io/badge/compileSdkVersion-27-yellow.svg?style=true" style="max-width:100%;">

## Navigation Drawer In Action
![app-in-action](https://user-images.githubusercontent.com/39665412/50330890-d8ca7d80-0537-11e9-8f29-8baa3f630343.gif)

## Tutorial
There is comprehensive documentation on how to implement a simple Navigation Drawer in Android [here](https://developer.android.com/training/implementing-navigation/nav-drawer), so I won't repeat it here. Instead, I will show how to add other features not covered in the documentation.
### 1. Navigate to other fragments
To handle menu item click events, read this part of the documentation under [Handle navigation click events](https://developer.android.com/training/implementing-navigation/nav-drawer#ListItemClicks).
In order to show another fragment on screen, first check the id of the menu item clicked using `getItemId()`, then use `getSupportFragmentManager()` to switch to another fragment:
```java
@Override
    public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
        switch (menuItem.getItemId()){
            case R.id.first_menu_id:
                getSupportFragmentManager().beginTransaction().replace(R.id.layout_container_id, new InstanceOfNewFragment())
                        .commit();
                // To close the drawer
                drawerLayout.closeDrawer(GravityCompat.START)
                break;
``` 
### 2. Remove hightlight of menu item
When a menu item clicked is not part of a group of single-selectable menu items, the previously selected item will remain highlighted even when its fragment is not displayed on screen. To deselect it, iterate through all the menu items and set their checked state to false:
```java
int noOfItems = navigationView.getMenu().size();
for (int i=0; i<noOfItems;i++){
    navigationView.getMenu().getItem(i).setChecked(false);
    }
``` 
### 3. Grouped Menu Items
If you want to have menu items grouped together and have a header on top, in your menu item xml file:
```xml
<item android:title="Communicate">
        <menu>
            <item
                android:id="@+id/nav_send_id"
                android:icon="@drawable/ic_send_black_24dp"
                android:title="Send" />
            <item
                android:id="@+id/nav_share_id"
                android:icon="@drawable/ic_publish_black_24dp"
                android:title="Share" />
            <item
                android:id="@+id/nav_photos_id"
                android:title="Photos"
                android:icon="@drawable/ic_photo_black_24dp"
                />
            <item
                android:id="@+id/nav_trash_id"
                android:title="Trash"
                android:icon="@drawable/ic_delete_black_24dp"
                />
        </menu>
```
Note that this removes the ability to make them single-selectable.

### 4. Back button closes navigation drawer
If you want the navigation drawer to close when the back button is pressed, override `onBackPressed()`:
```java
@Override
    public void onBackPressed() {
        //Checks if the navigation drawer is open -- If so, close it
        if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
            drawerLayout.closeDrawer(GravityCompat.START);
        }
        // If drawer is already close -- Do not override original functionality
        else {
            super.onBackPressed();
        }
    }
```
### 5. Display default fragment
`onCreate()` is called when the activity is initialized. By default, no fragment is displayed before the user selects a menu item in the drawer and hence the screen will be blank.

In order to show a default fragment when the app is first runned, check if the `savedInstanceState` passed to `onCreate()` is `null` by implementing to following lines of code in `onCreate()`:
```java
if (savedInstanceState == null){
        // Get the menu item at the itemIndex position
        MenuItem menuItem = navigationView.getMenu().getItem(itemIndex).setChecked(true);
        // Override
        onNavigationItemSelected(menuItem);
        }
```
### 6. Add custom widgets
#### - Switch Button
![switch-gif](https://user-images.githubusercontent.com/39665412/50329234-36a79700-0531-11e9-811b-ba08432cab4f.gif)

To add a switch button as a menu item in the navigation drawer, first create a seperate xml file for it with `android.support.v7.widget.SwitchCompat`. You do not necessarily need to give it an id.
```xml
<android.support.v7.widget.SwitchCompat                
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```
In the drawer menu xml file, use `app:actionLayout` to include it as part of a menu item:
```xml
    <item
        android:id="@+id/nav_darkmode_id"
        android:title="Dark Mode"
        android:icon="@drawable/ic_brightness_3_black_24dp"
        app:actionLayout="@layout/nav_drawer_darkmode_switch"           
        />
```
In java code, instantiate the switch button and attach a `setOnCheckedChangeListener` to it:
```java
private SwitchCompat darkModeSwitch;
@Override
    protected void onCreate(Bundle savedInstanceState) {
        darkModeSwitch = (SwitchCompat)navigationView.getMenu().findItem(R.id.nav_darkmode_id).getActionView();

        // Attach listener
        darkModeSwitch.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if (!isChecked){
                    // Switch is unchecked - Do something
                }
                else {
                   // Switch is checked - Do something
                }
            }
    }
```
## Dependencies / Built With 
- [Design Support Library](https://developer.android.com/reference/android/support/design/package-summary) - The Design package provides APIs to support adding material design components and patterns to your apps
- [CircleImageView](https://github.com/hdodenhof/CircleImageView) by hdodenhof - A circular ImageView for Android 
```java
dependencies {
  // Design Support Library
  implementation 'com.android.support:design:28.0.0'
  //CircleImageView
  implementation 'de.hdodenhof:circleimageview:2.2.0'
}
```
## License
```tex
MIT License

Copyright (c) 2018 Nicholas Gan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
``` 