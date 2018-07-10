# Base Navigation Module ![CI status](https://img.shields.io/badge/build-passing-brightgreen.svg)

Base Navigation Module is a Android library for centralizing all navigation, both for modules and for the project. 

We can also find different configurations so that we can customize the push or pop actions of fragments or activities.

## Installation

Add dependencies in .gradle
```
 implementation project(':basenavigationmodule')

```

## Introduction

In this library was implemented the basis of navigation for both tablet and phone. For this purpose, we have two types of navigation. One is a normal navigation, which means that it is an activity and the fragments are being replaced. The second type is a navigation with a bottom navigation, in which we have (in tablet), a screen divided in two (multi pane), in which a listed fragment (rootFragment) and a fragment of detail (detailFragment) are shown. 

## Bottom navigation

Before we start with the configuration we must take into account that we have navigation for tablet and phone. For this reason, in order to have a consistency in the elements, the following xml structure must be taken into consideration in the activity.

Phone:

![captura de pantalla 2018-07-09 a la s 10 54 15](https://user-images.githubusercontent.com/33325564/42462083-38875b0c-8370-11e8-836f-fe55790b9b58.png)


Tablet: 

![captura de pantalla 2018-07-09 a la s 11 01 40](https://user-images.githubusercontent.com/33325564/42462175-79d69ae6-8370-11e8-8d62-661d43aa1b1e.png)


As we can see from the images, both for tablet and phone we have patterns, these are:

a) rootContainer: we will call the first view to be displayed after pressing a menu root. For tablet help to know which view is shown on the left (root) and which ones go to the right of the screen.

b) detailContainer: we will call detail everything that comes after the root. This phone container is optional, but in tablet is mandatory, as all navigation will be in that frame.

c) containerFrames: this is very important, here the frames are distributed to give a tablet or phone format. E.g. if I want to have a split screen, I must first have a Linear layout that supports the splitting and is provided. 

Ej tablet: 

```xml
  <LineLayout
        android:id="@+id/containerFramesNavigation"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:orientation="horizontal"
        app:layout_constraintBottom_toTopOf="@+id/navigation"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:animateLayoutChanges="true"
        android:weightSum="100">

        <FrameLayout
            android:id="@+id/fragmentContainerList"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="40">
        </FrameLayout>>

        <FrameLayout
            android:id="@+id/fragmentContainer"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            app:layout_constraintBottom_toTopOf="@+id/navigation"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:layout_weight="60">

        </FrameLayout>

    </LinearLayout>
``` 
E.g. phone:

```xml
   <RelativeLayout
        android:id="@+id/containerFramesNavigation"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:orientation="horizontal"
        app:layout_constraintBottom_toTopOf="@+id/navigation"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:animateLayoutChanges="true">

        <FrameLayout
            android:id="@+id/fragmentContainerList"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        </FrameLayout>>

        <FrameLayout
            android:id="@+id/fragmentContainer"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent">
          <!-- optional -->

        </FrameLayout>

    </RelativeLayout>
```

c) BottomNavigation: It is the bottom navigation that has the menus 

For bottom navigation to work correctly, it must be extended from BaseModuleBottonNavigationActivity(), which generates mandatory parameters for its operation:

```java
 RetailActivity : BaseModuleBottonNavigationActivity() {

    override fun layoutBottonId(): Int = R.layout.activity_base_bottom_navigation

    override fun rootFrameContainer(): FrameLayout = findViewById<FrameLayout>(R.id.fragmentContainerList)

    override fun detailFrameContainer(): FrameLayout = findViewById<FrameLayout>(R.id.fragmentContainer)

    override fun containerFrameViewGroup(): ViewGroup = findViewById(R.id.containerFramesNavigation)

    override fun bottomNavigationView(): BottomNavigationView = findViewById<BottomNavigationView>(R.id.navigation)

    override fun emptyFragment(): Fragment = EmptyFragment.newInstance()

    override fun navigationActivityType(): NavigationWithType {

        return if (isTablet(this)) {
            NavigationWithType.NAVIGATION_TABLET_MULTIPANEL_WITH_BOTTOM
        } else {
            NavigationWithType.NAVIGATION_PHONE_WITH_BOTTOM
        }
    }

    override fun listRootDetailsFragment(): List<Fragment> {
        return listRootFragment
    }

    override fun positionRootFragment(): Int = HOME_OPTION_NAV_BOTTOM_POSITION
  }

```
   1) fun navigationActivityType(): NavigationWithType : Configure the type of device you are using (tablet or phone)

   2) fun listRootDetailsFragment(): List<Fragment> : This is the list of fragments that will be root for each bottom navigation menu. 
   This number of fragments must be equal to the number of menus found in the bottom navigation. You must also add the position (android:orderInCategory) in the file menu.xml. Ex:

menu.xml

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/menu_home"
        android:icon="@drawable/selector_ic_home"
        android:orderInCategory="0"
        />
    <item
        android:id="@+id/menu_buy"
        android:icon="@drawable/selector_ic_buy"
        android:orderInCategory="1"/>

    <item
        android:id="@+id/menu_my_account"
        android:icon="@drawable/selector_ic_member_center"
        android:orderInCategory="2"/>
</menu>

```
   3) fun positionRootFragment(): Int = Here the initial position of the bottom navigation is set.

   4) fun layoutBottonId(): Int = Here the layout corresponding to the activity is configured. 

   5) fun rootFrameContainer(): FrameLayout = Here you configure the frameLayout that contains the root fragment.

   6) fun detailFrameContainer(): FrameLayout = Here you configure the frameLayout that contains the detail fragment.

   7) fun containerFrameViewGroup(): ViewGroup = Here you configure container that includes the two frame layouts above. 
   It is recommended to use, if it is tablet Linear Layout with weights in each frame layout and RelativeLayout in case of phone.

   8) fun bottomNavigationView(): BottomNavigationView = Here you configure the bottom navigation view.

   9) fun emptyFragment(): Fragment = Here you configure an empty Fragment 


## Standa navigation

For normal navigation (replace fragments), it must extend from the BaseModuleNavigationActivity() class. And in turn, 4 mandatory methods must be implemented:

1) fun layoutContainerId(): Int : xml of the activity
2) fun frameContaier(): Frame Layout container for all fragments
3) fun initView(savedInstanceState: Bundle?) : is to start the view, here you must add the root fragment to start the activity with the created fragment
4) fun savedIntanceOfData(outState: Bundle?) : is to save any information that is needed when the view is onPause or restarted.

Example

```java
class SessionActivity : BaseModuleNavigationActivity() {

    override fun layoutContainerId(): Int = R.layout.activity_full_screen

    override fun frameContaier(): FrameLayout = findViewById<FrameLayout>(R.id.fragmentContainerFullScreen)

    override fun initView(savedInstanceState: Bundle?) {

        appComponent().inject(this)

        if (savedInstanceState == null) {
            val sessionFragment = SessionFragment()
            pushFragmentRootDetail(sessionFragment)
        }
    }

    override fun savedIntanceOfData(outState: Bundle?) {
        return
    }
}
```
## BaseNavigationHelperListener

BaseNavigationHelperListener is a class interface where you can use default navigation settings. 
These are functions of this class:

```java
    fun pushFragment(fragment: Fragment) {}
    fun pushFragmentRootDetail(fragment: Fragment) {}
    fun addFragment(fragment: Fragment, tag: String?) {}
    fun isShowingFragment(fragment: Fragment): Boolean {
        return true
    }

    fun changeCurrentPosition(position: Int) {}
    fun removeFragment(fragment: Fragment) {}
    fun removeAllCurrentStack() {}
    fun getLastFragmentAdded(): Fragment? {
        return null
    }

    fun getListBaseFragment(): List<Fragment> {
        return emptyList()
    }

    fun lasElemetofStack(): String {
        return String.empty()
    }

    fun popFromStack()
```

Definitions: 

1) pushFragment(fragment: Fragment) = receives a fragment and adds it to the navigation stack
2) pushFragmentRootDetail(fragment: Fragment) = receives a rootfragment and adds to top the navigation stack
3) addFragment(fragment: Fragment, tag: String?) = receives a fragment and adds it to the stack, the difference with a push is that it is added but there is no record with a tag.... it is recommended to use this function only when you add the same fragment that is already in the navigation stack
4) isShowingFragment(fragment: Fragment) = receives a fragment and returns it if it is in the navigation stack (true = if found, false = if not found)

5) changeCurrentPosition(position: Int) = change of position of the bottom navigation
6) removeFragment(fragment: Fragment) = receives a fragment and removes it
7) removeAllCurrentStack() = removes the entire stack except for the root fragment 


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
