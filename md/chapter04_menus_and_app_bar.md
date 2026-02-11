# 菜单和应用栏

设计应用程序的浏览模式是完善用户体验需要考虑的重要一环。如果浏览模式设计的不佳，用户无法快速定位所需的功能，或者出现错误定位使用户无法得到所需的功能，这些都会产生不好的用户体验。从安卓
3.0开始，安卓系统的应用浏览模式发生了较大的改变，引入了向上和返回的设计原则，并且提供了相应的设计组件，其中包含菜单、应用栏和浏览抽屉等。如果要实现准确的、一致的应用程序浏览，还需要理解浏览模式的原则，并且学会组件的使用。

## 菜单

在安卓中，支持菜单视图元素的关键类是android.view.Menu，每个活动都会关联一个这种类型的菜单对象。一个菜单对象包含了一些菜单项和子菜单。菜单项由android.view.MenuItem类表示，子菜单由android.view.SubMenu类表示。菜单项具有的属性包括名称、菜单项ID、分组ID、顺序等等。菜单的定义与用户界面的其他可视控件类似，可以通过XML文件定义菜单资源，保存在res目录下的menu文件夹中，在Java程序中可以通过ID来获取定义的对象进行操作。

### 菜单资源

对于所有类型的菜单，安卓提供了标准的XML格式来定义菜单项，所以除了在代码中实例化菜单之外，还可以在一个XML菜单资源中定义菜单和菜单项，然后在活动中使用资源ID加载菜单资源。使用XML资源来定义菜单是一种推荐的方式。使用XML资源来定义菜单有许多优点，例如可以更好地体现菜单的结构，可以使逻辑代码和菜单内容分离，可以为不同的平台版本、不同的屏幕尺寸等提供可以替换的菜单配置。菜单资源定义可通过
MenuInflater 进行扩充的应用菜单，包括选项菜单、上下文菜单和子菜单。

  - 文件位置：
    
      - res/menu/filename.xml，该文件名将用作资源 ID。

  - 编译后的资源数据类型：
    
      - 指向 Menu（或其子类）资源的资源指针。

  - 资源引用：
    
      - 在 Java 中：R.menu.filename
    
      - 在 XML 中：@\[package:\]menu.filename

**错误\!未找到引用源。**给出了使用XML文件定义菜单资源的示例。

\<?xml version="1.0" encoding="utf-8"?\>

\<[menu](http://developer.android.com/guide/topics/resources/menu-resource.html#menu-element)
xmlns:android="http://schemas.android.com/apk/res/android"\>

   
\<[item](http://developer.android.com/guide/topics/resources/menu-resource.html#item-element)
android:id="@\[+\]\[package:\]id/resource\_name"

          android:title="string"

          android:titleCondensed="string"

          android:icon="@\[package:\]drawable/drawable\_resource\_name"

          android:onClick="method name"

          android:showAsAction=\["ifRoom" | "never" | "withText" |
"always" | "collapseActionView"\]

         
android:actionLayout="@\[package:\]layout/layout\_resource\_name"

          android:actionViewClass="class name"

          android:actionProviderClass="class name"

          android:alphabeticShortcut="string"

          android:numericShortcut="string"

          android:checkable=\["true" | "false"\]

          android:visible=\["true" | "false"\]

          android:enabled=\["true" | "false"\]

          android:menuCategory=\["container" | "system" | "secondary" |
"alternative"\]

          android:orderInCategory="integer" /\>

   
\<[group](http://developer.android.com/guide/topics/resources/menu-resource.html#group-element)
android:id="@\[+\]\[package:\]id/resource name"

           android:checkableBehavior=\["none" | "all" | "single"\]

           android:visible=\["true" | "false"\]

           android:enabled=\["true" | "false"\]

           android:menuCategory=\["container" | "system" | "secondary" |
"alternative"\]

           android:orderInCategory="integer" \>

       
\<[item](http://developer.android.com/guide/topics/resources/menu-resource.html#item-element)
/\>

    \</group\>

   
\<[item](http://developer.android.com/guide/topics/resources/menu-resource.html#item-element)
\>

       
\<[menu](http://developer.android.com/guide/topics/resources/menu-resource.html#menu-element)\>

         
\<[item](http://developer.android.com/guide/topics/resources/menu-resource.html#item-element)
/\>

        \</menu\>

    \</item\>

\</menu\>

码 ‑1定义菜单资源的语法

下面针对XML文件定义菜单资源时使用的一些元素进行说明。

  - \<menu\>

此元素用来定义菜单，用来包含菜单项。必须有一个\<menu\>元素作为菜单资源XML文件的根元素，其中可以包含一个或多个\<item\>和\<group\>元素

  - \<item\>

此元素用来定义菜单项，每个\<item\>都表示一个菜单项，而且其还可以包含一个内嵌的\<menu\>元素，用来创建子菜单。另外，经常使用下面几种\<item\>元素属性来定义菜单项的显示和行为：

  - android:id 表示菜单项的唯一资源ID，用来识别菜单项

  - android:icon 表示菜单项的显示图标，可以指定一个图片资源

  - android:title 表示菜单项的显示标题，这里指定一个字符串资源

  - android:onClick方法名称。点击此菜单项时调用的方法。此方法必须在 Activity 中声明为公共方法，并接受
    MenuItem 作为其唯一参数，该参数指示所点击的项。此方法优先于对 onOptionsItemSelected() 的标准回调。

  - android:showAsAction关键字。指示此项应在应用栏中显示为操作项的时机和方式。菜单项只有在 Activity
    包含应用栏时才能显示为操作项，有效值：

| 值                  | 说明                                                                                                |
| ------------------ | ------------------------------------------------------------------------------------------------- |
| ifRoom             | 只有在应用栏中有空间的情况下，才将此项放置其中。如果没有足够的空间来容纳标记为 "ifRoom" 的所有项，则 orderInCategory 值最低的项会显示为操作，其余项将显示在溢出菜单中。 |
| withText           | 此外，还会随操作项添加标题文本（由 android:title 定义）。可以将此值与某个其他值一起作为标记集添加，用竖线 | 分隔。                                |
| never              | 不得将此项放在应用栏中，而应将其列在应用栏的溢出菜单中。                                                                      |
| always             | 始终将此项放在应用栏中。除非此项必须始终显示在操作栏中，否则请勿使用该值。将多个项设置为始终显示为操作项，会导致它们与应用栏中的其他界面重叠。                           |
| collapseActionView | 与此操作项相关联的操作视图（由 android:actionLayout 或 android:actionViewClass 声明）是可收起的，在 API 级别 14 中引入。          |

表 4‑1

  - \<group\>

此元素是一个可选的、不可见的，可以用来对菜单项进行分类，目的是使它们可以共享相同的属性，例如激活状态和可见性。

  - android:checkableBehavior，关键字。组的可勾选行为类型。有效值：

| 值      | 说明             |
| ------ | -------------- |
| none   | 不可勾选           |
| all    | 可以勾选所有项（使用复选框） |
| single | 只能勾选一项（使用单选按钮） |

表 4‑2

  - android:visible，布尔值。如果组可见，则为“true”。

  - android:enabled，布尔值。如果组为启用状态，则为“true”。

  - android:menuCategory，关键字。对应于 Menu CATEGORY\_\*
    常量的值，这些常量用于定义组的优先级。有效值：

| 值           | 说明                  |
| ----------- | ------------------- |
| container   | 这类组归属于容器。           |
| system      | 这类组由系统提供。           |
| secondary   | 这类组是用户提供的次要（不常用）选项。 |
| alternative | 这类组是对当前显示的数据的替代操作。  |

表 4‑3

  - android:orderInCategory，整数。项在类别中的默认顺序。

保存在 res/menu/example\_menu.xml 的 XML 文件：

\<menu xmlns:android="http://schemas.android.com/apk/res/android"

xmlns:app="http://schemas.android.com/apk/res-auto"\>

\<item android:id="@+id/item1"

android:title="@string/item1"

android:icon="@drawable/group\_item1\_icon"

app:showAsAction="ifRoom|withText"/\>

\<group android:id="@+id/group"\>

\<item android:id="@+id/group\_item1"

android:onClick="onGroupItemClick"

android:title="@string/group\_item1"

android:icon="@drawable/group\_item1\_icon" /\>

\<item android:id="@+id/group\_item2"

android:onClick="onGroupItemClick"

android:title="@string/group\_item2"

android:icon="@drawable/group\_item2\_icon" /\>

\</group\>

\<item android:id="@+id/submenu"

android:title="@string/submenu\_title"

app:showAsAction="ifRoom|withText" \>

\<menu\>

\<item android:id="@+id/submenu\_item1"

android:title="@string/submenu\_item1" /\>

\</menu\>

\</item\>

\</menu\>

码 ‑2

以下应用代码会通过 onCreateOptionsMenu(Menu) 回调扩充菜单，还会声明其中两个项的点击回调：

public boolean onCreateOptionsMenu(Menu menu) {

MenuInflater inflater = getMenuInflater();

inflater.inflate(R.menu.example\_menu, menu);

return true;

}

public void onGroupItemClick(MenuItem item) {

}

码 ‑3

如果需要增加子菜单，需要在\<item\>元素中包含\<menu\>元素，子菜单可以起到将应用程序功能按照主题进行分类的作用。例如在Microsoft
Office套件应用的菜单条中都有“文件”、“编辑”等子菜单。子菜单中菜单选项的定义与菜单类似，元素和属性的应用也相同，**错误\!未找到引用源。**给出了使用XML资源文件定义子菜单的简单例子。

\<?xml version="1.0" encoding="utf-8"?\>

\<menu xmlns:android="http://schemas.android.com/apk/res/android"\>

\<item android:id="@+id/file"

android:title="@string/file" \>

\<\!-- "file" submenu --\>

\<menu\>

\<item android:id="@+id/create\_new"

android:title="@string/create\_new" /\>

\<item android:id="@+id/open"

android:title="@string/open" /\>

\</menu\>

\</item\>

\</menu\>

码 ‑4 使用XML文件定义子菜单

如果要将定义好的菜单资源加载到活动中，需要使用[MenuInflater.inflate()](http://developer.android.com/reference/android/view/MenuInflater.html#inflate\(int,%20android.view.Menu\))方法，在下面的章节中，介绍每种菜单的加载方式。

### 菜单类型

安卓支持丰富的菜单类型，包括常规的菜单、子菜单、上下文菜单、图标菜单、二级菜单和替代菜单。安卓
3.0推出了应用栏，可以与菜单进行交互，安卓4.0已经推出弹出式菜单，可以随时响应按钮点击或任何其他界面事件。安卓提供的菜单有如下三种基本类型：

  - 选项菜单

选项菜单是一个活动菜单项的主要集合，如果在这里加入操作，将会影响应用程序的全局。如果使用安卓2.3或者之前版本开发，用户可以使用菜单键打开选项菜单，但是对于安卓3.0或者更高的版本来说，选项菜单中的菜单项目是通过应用栏与其它屏幕动作项目一起展现的。从安卓3.0开始，一些设备已经不支持菜单键，需要使用应用栏来开发应用。

  - 上下文菜单

当长按某个视图或视图元素后出现的浮动菜单，菜单中包含的动作是与用户所选择视图元素相关的。在安卓3.0和更高版本上进行开发，可以在选定的内容上使用上下文操作模式，显示相应的操作。这种模式在屏幕上方的操作条中显示影响所选的内容的操作项，并允许用户选择多个项目。

  - 弹出菜单

弹出菜单被固定在调用菜单的视图元素上，并且在一个垂直列表中显示菜单项目。

在安卓中，这些菜单都可以在XML资源文件中定义，并通过菜单资源文件中的ID加载到Java程序中。

#### 选项菜单

在安卓
SDK中，由于每个活动都会关联一个菜单，所以不需要从头开始创建一个菜单对象。安卓的菜单创建，具体是由活动的onCreateOptionsMenu()回调方法来实现的，选项菜单的创建也可以由这个回调方法来实现。选项菜单中包含的动作和选项与当前活动上下文相关，并且根据安卓系统版本的不同，其显示的位置也不同。如果使用安卓
2.3.x（API level
10）或者更低的版本，当用户按菜单键时，选项菜单的内容显示在屏幕的底部，可以最多显示6个带有图标按钮的无滚动条窗体；如果菜单项超过6个就需要使用扩展菜单项，这样这个窗体的最后一个按钮变成了“More”，选中后会弹出一个包含多个菜单项的列表，可能还带有滚动条。**图
4‑1**中的示例显示了安卓 2.3.x的选项菜单样式。

![](md/media/chapter04_menus_and_app_bar/media/image1.png)

**图** **4‑1**安卓 2.3.x**选项菜单样式**

如果使用安卓 3.0（API level
11）或者更高的版本，用户可以在应用栏中使用选项菜单的菜单项。默认情况下，系统将所有的菜单项作为应用栏的溢出操作，用户可以点击应用栏最右边的溢出操作图标显示没有显示的菜单项；如果手机有菜单键，对于不在应用栏中显示的菜单项，用户按菜单键则会看到剩余的菜单项。**图
4‑2**示例显示了安卓3.0的选项菜单样式。

![](md/media/chapter04_menus_and_app_bar/media/image2.png)

**图** **4‑2** 安卓 3.0以上**选项菜单样式**

选项菜单选项既可以在活动中声明，也可以在另一种更灵活的图形组件片段中声明。如果活动和片段都为选项菜单声明了菜单项，而且合并在界面中，那么活动中的菜单项优先显示，然后才按顺序把片段中菜单项添加到活动中。如果要设定菜单项的顺序，也可以在\<item\>元素中添加android:orderInCategory的属性，重新按次序添加菜单项。在活动中，通过覆盖其onCreateOptionsMenu()方法来指定选项菜单，具体实现加载菜单；在片段中，也是覆盖其onCreateOptionsMenu()方法，通过菜单资源中定义的菜单ID，获取菜单对象，赋值给声明的菜单类变量（见码
4‑5）。

@Override

public boolean onCreateOptionsMenu(Menu menu) {

MenuInflater inflater = getMenuInflater();

inflater.inflate(R.menu.game\_menu, menu);

return true;

}

码 ‑5在onCreateOptionsMenu()方法中获取菜单对象

在码
4‑5中，getMenuInflater()方法返回了一个MenuInflater对象，用此对象来调用inflate()方法，将菜单资源填充到菜单对象中。一旦菜单项被加载，onCreateOptionsMenu()方法应该返回true，使菜单可见。如果此方法返回false，菜单是不可见的。对于安卓
2.3.x和更低的版本来说，这个方法是在用户第一次打开菜单的时候由系统执行的；而对于安卓
3.0和更高的版本来说，由于要在应用栏中显示菜单项，系统在启动活动时就调用此方法。

系统调用 onCreateOptionsMenu() 后，会保留填充的Menu实例。除非菜单由于某些原因而失效，否则系统不会再次调用
onCreateOptionsMenu()。但是，只应使用 onCreateOptionsMenu() 来创建初始菜单状态，而不应使用它在
Activity 生命周期中执行任何更改。如需根据在 Activity 生命周期中发生的事件修改选项菜单，可通过
onPrepareOptionsMenu() 方法执行此操作。此方法传递 Menu
对象（因为该对象目前存在），以便能够对其进行修改，如添加、移除或停用菜单项。片段也提供
onPrepareOptionsMenu() 回调。在 Android 2.3.x
及更低版本中，每当用户打开选项菜单时（按“菜单”按钮），系统均会调用
onPrepareOptionsMenu()。在 Android 3.0
及更高版本中，当菜单项显示在应用栏中时，系统会将选项菜单视为始终处于打开状态。发生事件时，如果要执行菜单更新，必须调用
invalidateOptionsMenu() 来请求系统调用
onPrepareOptionsMenu()。在安卓应用程序中，也可以使用Menu类提供的add()方法动态增加菜单项。Menu类的add()方法的参数说明如下：

  - int groupId：分组标识，其值相同的菜单项可以归为一组。

  - int itemId ：菜单项ID，代表菜单项的唯一编号，使用这个编号可以找到对应的菜单项。

  - int order：菜单项排列顺序（代表的是菜单项显示顺序，默认值是0），其值越小越表示越重要，优先显示。

  - CharSequence
    title：String类型的菜单项标题，表示需要在界面选项中显示的文字。除了使用字符串，还可以使用一个字符串资源，通过R.java文件常量文件。

分组ID、菜单项ID和排列属性都是可选的，如果不想特别指定的话可以使用Menu.NONE。码
4‑6给出了一个简单的例子，示例了如何使用add()方法动态加载三个菜单项。

@Override

public boolean onCreateOptionsMenu(Menu menu){

//call the base class to include system menus

super.onCreateOptionsMenu(menu);

menu.add(0 // Group

,1 // item id

,0 //order

,"append"); // title

menu.add(0,2,1,"item2");

menu.add(0,3,2,"clear");

return true;

}

码 ‑6 使用add()方法动态加载菜单

对于菜单选项的点击事件，安卓系统使用专门的方法来进行处理。当用户点击菜单项时，系统会调用活动的onOptionsItemSelected()方法，并且将用户点击的菜单项对象（MenuItem）传递给该方法。在这个方法中，可以用getItemId()方法来获取菜单项的资源ID，针对不同的菜单项，进行不同的操作。码
4‑7中，通过getItemId()获取菜单ID后，通过判断ID不同的值，实现在用户点击菜单项后，指定的文本框中显示出所点击的菜单选项标题。这个例子为了简单，编写的事件响应操作代码都相同，但实际应用程序中，对应于每个菜单选项的事件处理，都对应了其响应的功能实现代码或方法。

@Override

public boolean onOptionsItemSelected(MenuItem item) {

TextView txt=(TextView)findViewById(R.id.txt);

switch(item.getItemId()) {

case 1:

txt.setText("you clicked on item "+item.getTitle());

return true;

case 2:

txt.setText("you clicked on item "+item.getTitle());

return true;

case 3:

txt.setText("you clicked on item "+item.getTitle());

return true;

}

return super.onOptionsItemSelected(item);

}

码 ‑7 使用onOptionsItemSelected()方法处理菜单项事件

如果被选的菜单项得到成功处理，则onOptionsItemSelected()返回true值，否则需要调用父类的onOptionsItemSelected()方法继续处理。如果活动中包含片段，那么系统首先会调用活动中onOptionsItemSelected()方法，然后才是每个片段中的方法onOptionsItemSelected()，直到有一个方法返回true值，否则所有的onOptionsItemSelected()方法都会被调用了。

除了使用onOptionsItemSelected()之外，还可以使用监听器来响应和处理事件，这种方式需要实现OnMenuItemClickListner接口以及其onMenuItemClick()方法（码
4‑1）。

public class MyResponse implements OnMenuClickListener {

@override

boolean onMenuItemClick(MenuItem item) {

// coding

return true;

}

}

码 4‑1 定义OnMenuItemClickListner监听器

与传统的Java事件处理程序类似，安卓应用程序在使用监听器处理事件时，也需要对监听器进行注册。对于码
4‑1定义的监听器，可以使用下面的代码注册：

MyResponse myResponse = new MyResponse(...);

menuItem.setOnMenuItemClickListener(myResponse);

码 ‑8

如果同时定义了onOptionsItemSelected()方法和监听器处理方法，点击菜单项就会首先执行监听器中的onMenuItemClick()方法。如果onMenuItemClick()方法返回值为true，则表示点击菜单项的事件处理已经完成，就不会执行onOptionsItemSelected()方法；如果返回值为false，才执行onOptionsItemSelected()方法。另外，在菜单资源文件中安卓系统还为菜单项提供了android:onClick属性，可以定义菜单项处理点击事件的方法。如果多个活动都拥有相同的菜单，可以定义一个只有onCreateOptionsMenu()和onOptionsItemSelected()方法的活动，在其中实现这个菜单，然后让其他类来继承该类。如果想在子类中添加新的菜单项，则只需重写onCreateOptionsMenu()方法，并且调用super.onCreateOptionsMenu()方法创建父类的菜单项，然后再使用add()方法添加新的菜单项。但是onCreateOptionsMenu()方法是用来初始化菜单的状态，只能在菜单刚被创建时才会执行，所以不能用这个方法而在活动的生命周期中修改菜单。如果要想动态改变选项菜单，就要实现onPrepareOptionsMenu()方法，系统会将当前使用菜单对象传递给该方法，可以在这个方法中修改菜单。安卓2.3或更低的版本中，系统会在每次菜单打开的时候调用一次onPrepareOptionsMenu()方法；而在安卓3.0及以上版本，由于选项菜单是在应用栏中显示的，此选项菜单总是打开的，所以必须调用invalidateOptionsMenu()方法请求系统调用onPrepareOptionsMenu()方法执行更新操作。下面使用一个例子，来说明一下在应用程序中如何实现选项菜单。

import android.content.Intent;

import android.os.Bundle;

import android.view.Menu;

import android.view.MenuItem;

import android.view.SubMenu;

import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import com.example.ch04.R;

public class OptionMenusActivity extends AppCompatActivity {

//Initialize this in onCreateOptions

Menu myMenu = null;

/\*\*

\* Called when the activity is first created.

\*/

@Override

public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.menu\_options);

}

@Override

public boolean onCreateOptionsMenu(Menu menu) {

//call the parent to attach any system level menus

super.onCreateOptionsMenu(menu);

this.myMenu = menu;

//add a few normal menus

addRegularMenuItems(menu);

//add a few secondary menus

addSecondaryMenuItems(menu);

addSubMenu(menu);

//it must return true to show the menu

//if it is false menu won't show

return true;

}

private void addRegularMenuItems(Menu menu) {

//Secondary items are shown just like everything else

int base = Menu.FIRST; // value is 1

MenuItem item1 = menu.add(base, base, base, "Simple Menu");

menu.add(base, base + 1, base + 1, "XML Menu");

menu.add(base, base + 2, base + 2, "Clear");

menu.add(base, base + 3, base + 3, "Hide secondary");

menu.add(base, base + 4, base + 4, "Show secondary");

menu.add(base, base + 5, base + 5, "Enable secondary");

menu.add(base, base + 6, base + 6, "Disable secondary");

menu.add(base, base + 7, base + 7, "Check secondary");

MenuItem item8 = menu.add(base, base + 8, base + 8, "Uncheck
secondary");

//This will show the icon

//It might obscure the text

item1.setIcon(R.drawable.balloons);

//But this does not

item8.setIcon(R.drawable.balloons);

}

private void addSecondaryMenuItems(Menu menu) {

//Secondary items are shown just like everything else

int base = Menu.CATEGORY\_SECONDARY;

menu.add(base, base + 1, base + 1, "Sec. Item 1");

menu.add(base, base + 2, base + 2, "Sec. Item 2");

menu.add(base, base + 3, base + 3, "Sec. Item 3");

menu.add(base, base + 3, base + 3, "Sec. Item 4");

menu.add(base, base + 4, base + 4, "Sec. Item 5");

}

private void addSubMenu(Menu menu) {

//Secondary items are shown just like everything else

int base = Menu.FIRST + 100;

SubMenu sm = menu.addSubMenu(base, base + 1, Menu.NONE, "Submenu");

MenuItem item1 = sm.add(base, base + 2, base + 2, "Sub Item1");

sm.add(base, base + 3, base + 3, "Sub Item2");

sm.add(base, base + 4, base + 4, "Sub Item3");

//work the icons

//submenu item icons are not supported

item1.setIcon(R.drawable.icon48x48\_2);

sm.setIcon(R.drawable.icon48x48\_1);

}

@Override

public boolean onOptionsItemSelected(MenuItem item) {

if (item.getItemId() == 1) {

appendText("\\nSimple Menu");

} else if (item.getItemId() == 2) {

this.appendMenuItemText(item);

Intent intent = new Intent(this, XMLMenusActivity.class);

this.startActivity(intent);

} else if (item.getItemId() == 3) {

emptyText();

} else if (item.getItemId() == 4) {

//hide secondary

this.appendMenuItemText(item);

this.myMenu.setGroupVisible(Menu.CATEGORY\_SECONDARY, false);

} else if (item.getItemId() == 5) {

//show secondary

this.appendMenuItemText(item);

this.myMenu.setGroupVisible(Menu.CATEGORY\_SECONDARY, true);

} else if (item.getItemId() == 6) {

//enable secondary

this.appendMenuItemText(item);

this.myMenu.setGroupEnabled(Menu.CATEGORY\_SECONDARY, true);

} else if (item.getItemId() == 7) {

//disable secondary

this.appendMenuItemText(item);

this.myMenu.setGroupEnabled(Menu.CATEGORY\_SECONDARY, false);

} else if (item.getItemId() == 8) {

//check secondary

this.appendMenuItemText(item);

this.myMenu.setGroupCheckable(Menu.CATEGORY\_SECONDARY, true, false);

} else if (item.getItemId() == 9) {

//uncheck secondary

this.appendMenuItemText(item);

this.myMenu.setGroupCheckable(Menu.CATEGORY\_SECONDARY, false, false);

} else {

this.appendMenuItemText(item);

}

//should return true if the menu item

//is handled

return true;

}

private TextView getTextView() {

TextView tv =

(TextView) this.findViewById(R.id.textViewId);

return tv;

}

public void appendText(String text) {

TextView tv =

(TextView) this.findViewById(R.id.textViewId);

tv.setText(tv.getText() + text);

}

private void appendMenuItemText(MenuItem menuItem) {

String title = menuItem.getTitle().toString();

TextView tv =

(TextView) this.findViewById(R.id.textViewId);

tv.setText(tv.getText() + "\\n" + title + ":" + menuItem.getItemId());

}

private void emptyText() {

TextView tv =

(TextView) this.findViewById(R.id.textViewId);

tv.setText("");

}

}

码 ‑9 OptionMenusActivity

![](md/media/chapter04_menus_and_app_bar/media/image3.png)

图 ‑3 选项菜单

1.  **上下文菜单**

在桌面系统的用户界面中，当用户使用鼠标右键点击界面视图元素时，桌面系统就会弹出与此视图元素相关的动作列表。这个功能非常方便，用户可以很容易找到与视图元素相关的功能。提供这种功能的菜单，成为上下文菜单。安卓系统也支持相同的设计模式，但由于用户交互的设备不同，操作时界面的响应有所不同。安卓的上下文菜单可以通过触摸屏操作调出。当用户按住触摸屏上的视图元素保持一段时间，就可以调出相关动作列表的上下文菜单。安卓系统可以为任何视图提供上下文菜单，但是通常在ListView、GridView中的项目上使用，或者其它视图集合中项目。安卓系统定义了两种模式的上下文菜单：

  - 悬浮上下文模式

如果用户在视图元素上执行一个长点击（按住并保持）事件，上下文菜单项浮动列表会弹出，类似对话框，显示在原有视图的上面，覆盖原有的部分用户界面（见图
4‑4左图）。用户可以每次在浮动菜单中选择一个可执行的动作。

  - 关联操作模式

这种模式是ActionMode的系统实现，可以在屏幕顶部应用栏显示上下文，其中的菜单项是影响所选视图元素的动作。当这种模式被激活，用户可以在使用上下文菜单的应用栏中选择一个或多个动作。但是，这种模式只有在安卓3.0或者更高版本可用，是使用上下文菜单的推荐模式。

![](md/media/chapter04_menus_and_app_bar/media/image4.png)

图 ‑4 菜单模式

上下文菜单的加载与选项菜单类似，都是在活动中通过特定方法中创建和加载，但具体在onCreateContextMenu()方法中实现，而不是在onCreateOptionsMenu()方法中实现。在实现onCreateContextMenu()方法时，上下文菜单所usuo依赖的视图元素通过参数指定。上下文菜单的加载与选项菜单在加载时有所不同。onCreateOptionsMenu()方法在每个活动（或者片段）启动时自动调用。因为不是界面上的所有视图元素都需要上下文视图元素，只有通过registerForContextMenu(view)方法注册的视图元素，才有可能创建对应的上下文菜单。因此只有用户长点击某个视图元素后才需要执行onCreateContextMenu()方法。下面使用的例子，来说明一下在应用程序中如何实现上下文菜单。

import android.graphics.Color;

import android.os.Bundle;

import android.view.ContextMenu;

import android.view.ContextMenu.ContextMenuInfo;

import android.view.MenuItem;

import android.view.View;

import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import com.example.ch04.R;

public class ContextMenuSimpleActivity extends AppCompatActivity {

final int MENU\_COLOR\_RED = 1;

final int MENU\_COLOR\_GREEN = 2;

final int MENU\_COLOR\_BLUE = 3;

final int MENU\_SIZE\_22 = 4;

final int MENU\_SIZE\_26 = 5;

final int MENU\_SIZE\_30 = 6;

TextView tvColor, tvSize;

/\*\* Called when the activity is first created. \*/

@Override

public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.context\_menu\_simple);

tvColor = (TextView) findViewById(R.id.tvColor);

tvSize = (TextView) findViewById(R.id.tvSize);

// context menu should be created for tvColor and tvSize

registerForContextMenu(tvColor);

registerForContextMenu(tvSize);

}

@Override

public void onCreateContextMenu(ContextMenu menu, View v,

ContextMenuInfo menuInfo) {

// TODO Auto-generated method stub

switch (v.getId()) {

case R.id.tvColor:

menu.add(0, MENU\_COLOR\_RED, 0, "Red");

menu.add(0, MENU\_COLOR\_GREEN, 0, "Green");

menu.add(0, MENU\_COLOR\_BLUE, 0, "Blue");

break;

case R.id.tvSize:

menu.add(0, MENU\_SIZE\_22, 0, "22");

menu.add(0, MENU\_SIZE\_26, 0, "26");

menu.add(0, MENU\_SIZE\_30, 0, "30");

break;

}

}

@Override

public boolean onContextItemSelected(MenuItem item) {

// TODO Auto-generated method stub

switch (item.getItemId()) {

// menu items for tvColor

case MENU\_COLOR\_RED:

tvColor.setTextColor(Color.RED);

tvColor.setText("Text color = red");

break;

case MENU\_COLOR\_GREEN:

tvColor.setTextColor(Color.GREEN);

tvColor.setText("Text color = green");

break;

case MENU\_COLOR\_BLUE:

tvColor.setTextColor(Color.BLUE);

tvColor.setText("Text color = blue");

break;

// menu items for tvSize

case MENU\_SIZE\_22:

tvSize.setTextSize(22);

tvSize.setText("Text size = 22");

break;

case MENU\_SIZE\_26:

tvSize.setTextSize(26);

tvSize.setText("Text size = 26");

break;

case MENU\_SIZE\_30:

tvSize.setTextSize(30);

tvSize.setText("Text size = 30");

break;

}

return super.onContextItemSelected(item);

}

}

码 ‑10 上下文模式菜单

如果被选的菜单项被成功处理，则onContextItemSelected()返回true值，否则需要调用父类的onContextItemSelected()方法继续处理。与选项菜单类似，如果活动中包含片段，那么活动将首先执行自己的这个方法，如果返回值为false，则通过调用super.onContextItemSelected(item)方法，点击事件将会在每个片段中的onContextItemSelected()方法中传递，按照片段被添加的顺序一个接着一个，直到返回true或者全部执行完为止。

![](md/media/chapter04_menus_and_app_bar/media/image5.png)

图 ‑5 上下文菜单

关联操作模式是 ActionMode
的一种系统实现，它将用户互动的重点放在执行关联操作上。当用户通过选择菜单项启用此模式时，屏幕顶部会出现“关联操作栏”，显示用户可对当前所选菜单项执行的操作。启用此模式后，如果允许用户可以选择多个菜单项，取消选择菜单项，以及继续在活动内导航（在您允许的最大范围内）。当用户取消选择所有菜单项、按“返回”按钮或选择操作栏左侧的“完成”操作时，该操作模式将会停用，而关联操作栏也会消失。注意：关联操作栏不一定与应用栏相关联。尽管表面上看来关联操作栏取代了应用栏的位置，但事实上二者独立运行。对于提供关联操作的视图，当出现以下两个事件（或之一）时，通常应调用关联操作模式：

  - 用户长按视图。

  - 用户选中复选框或视图内类似的界面组件。

应用如何调用关联操作模式以及如何定义每个操作的行为取决于您的设计。设计基本上分为两种：

  - 针对单个任意视图的关联操作。

  - 针对 ListView 或 GridView 中菜单项组的批处理关联操作（允许用户选择多个菜单项并针对所有菜单项执行操作）。

下面几部分介绍了每种场景所需的设置。

如果只想在用户选择特定视图时调用关联操作模式，应该：

  - 实现 ActionMode.Callback
    接口。在其回调方法中，您可以为关联操作栏指定操作、响应操作项的点击事件，以及处理该操作模式的其他生命周期事件。

  - 当需要显示操作栏时（例如，当用户长按视图时），请调用 startActionMode()。

例如：

private ActionMode.Callback actionModeCallback = new
ActionMode.Callback() {

// Called when the action mode is created; startActionMode() was called

@Override

public boolean onCreateActionMode(ActionMode mode, Menu menu) {

// Inflate a menu resource providing context menu items

MenuInflater inflater = mode.getMenuInflater();

inflater.inflate(R.menu.context\_menu, menu);

return true;

}

// Called each time the action mode is shown. Always called after
onCreateActionMode, but

// may be called multiple times if the mode is invalidated.

@Override

public boolean onPrepareActionMode(ActionMode mode, Menu menu) {

return false; // Return false if nothing is done

}

// Called when the user selects a contextual menu item

@Override

public boolean onActionItemClicked(ActionMode mode, MenuItem item) {

switch (item.getItemId()) {

case R.id.menu\_share:

shareCurrentItem();

mode.finish(); // Action picked, so close the CAB

return true;

default:

return false;

}

}

// Called when the user exits the action mode

@Override

public void onDestroyActionMode(ActionMode mode) {

actionMode = null;

}

};

码 ‑11

请注意，这些事件回调与选项菜单的回调几乎完全相同，只是其中每个回调还会传递与事件相关联的 ActionMode 对象。可以使用
ActionMode API 对悬浮菜单进行各种更改，例如使用 setTitle() 和 setSubtitle()
修改标题和副标题（这有助于指示需选择多少个菜单项）。注意，当系统销毁该操作模式时，上述示例会将
actionMode 变量设置为 null。在下一步中，将了解如何初始化该变量，以及保存活动或 片段中的成员变量有何作用。调用
startActionMode() 以便适时启用关联操作模式，例如响应对视图的长按操作：

someView.setOnLongClickListener(new View.OnLongClickListener() {

// Called when the user long-clicks on someView

public boolean onLongClick(View view) {

if (actionMode \!= null) {

return false;

}

// Start the CAB using the ActionMode.Callback defined above

actionMode = getActivity().startActionMode(actionModeCallback);

view.setSelected(true);

return true;

}

});

码 ‑12 视图的长按操作

当用 startActionMode() 时，系统会返回已创建的
ActionMode。通过将其保存在成员变量中，您可以通过更改关联操作栏来响应其他事件。在上述示例中，ActionMode
用于在启动该操作模式前检查成员是否为 null，从而确保当 ActionMode 实例已激活时不再重建该实例。

![](md/media/chapter04_menus_and_app_bar/media/image6.png)

图 ‑6长按关联操作模式

如果在 ListView 或 GridView（或 AbsListView 的其他扩展）中有一组菜单项，且希望允许用户执行批处理操作，应该：

  - 实现 AbsListView.MultiChoiceModeListener 接口，并使用
    setMultiChoiceModeListener()
    为视图组设置该接口。在监听器的回调方法中，您可以为关联操作栏指定操作、响应操作项的点击事件，以及处理从
    ActionMode.Callback 接口继承的其他回调。

  - 使用 CHOICE\_MODE\_MULTIPLE\_MODAL 参数调用 setChoiceMode()。

import android.database.Cursor;

import android.os.Bundle;

import android.view.ActionMode;

import android.view.Menu;

import android.view.MenuInflater;

import android.view.MenuItem;

import android.widget.AbsListView;

import android.widget.ListView;

import androidx.appcompat.app.AppCompatActivity;

import com.example.ch04.R;

public class RemindersActivity extends AppCompatActivity {

private ListView mListView;

private RemindersDbAdapter mDbAdapter;

private RemindersSimpleCursorAdapter mCursorAdapter;

@Override

protected void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.activity\_reminders);

mListView = (ListView) findViewById(R.id.reminders\_list\_view);

mListView.setDivider(null);

mDbAdapter = new RemindersDbAdapter(this);

mDbAdapter.open();

if (savedInstanceState == null) {

//Clear all data

mDbAdapter.deleteAllReminders();

//Add some data

insertSomeReminders();

}

Cursor cursor = mDbAdapter.fetchAllReminders();

//from columns defined in the db

String\[\] from = new String\[\]{

RemindersDbAdapter.COL\_CONTENT};

//to the ids of views in the layout

int\[\] to = new int\[\]{R.id.row\_text};

mCursorAdapter = new
RemindersSimpleCursorAdapter(RemindersActivity.this,

R.layout.reminders\_row,

cursor,

from,

to,

0);

//the cursorAdapter (controller) is now updating the listView (view)

//with data from the db(model)

mListView.setAdapter(mCursorAdapter);

mListView.setChoiceMode(ListView.CHOICE\_MODE\_MULTIPLE\_MODAL);

mListView.setSelection(R.color.light\_grey);

mListView.setMultiChoiceModeListener(new
AbsListView.MultiChoiceModeListener() {

@Override

public void onItemCheckedStateChanged(ActionMode mode, int position,
long id, boolean

checked) {

final int checkedCount = mListView.getCheckedItemCount();

switch (checkedCount) {

case 0:

mode.setSubtitle(null);

break;

case 1:

mode.setSubtitle("1 item selected");

break;

default:

mode.setSubtitle("" + checkedCount + " items selected");

break;

}

}

@Override

public boolean onCreateActionMode(ActionMode mode, Menu menu) {

MenuInflater inflater = mode.getMenuInflater();

inflater.inflate(R.menu.cam\_menu, menu);

return true;

}

@Override

public boolean onPrepareActionMode(ActionMode mode, Menu menu) {

return false;

}

@Override

public boolean onActionItemClicked(ActionMode mode, MenuItem item) {

switch (item.getItemId()) {

case R.id.menu\_item\_delete\_reminder:

for (int nC = mCursorAdapter.getCount() - 1; nC \>= 0; nC--) {

if (mListView.isItemChecked(nC)) {

mDbAdapter.deleteReminderById(getIdFromPosition(nC));

}

}

mode.finish();

mCursorAdapter.changeCursor(mDbAdapter.fetchAllReminders());

return true;

}

return false;

}

@Override

public void onDestroyActionMode(ActionMode mode) {

}

});

}

private int getIdFromPosition(int nC) {

return (int) mCursorAdapter.getItemId(nC);

}

private void insertSomeReminders() {

mDbAdapter.createReminder("Set in the layout of list's row", true);

mDbAdapter.createReminder("Override onItemCheckedStateChanged", false);

mDbAdapter.createReminder("Usually a LinearLayout", false);

mDbAdapter.createReminder("Understand what it does exactly", false);

mDbAdapter.createReminder("Modify the below according needs", false);

mDbAdapter.createReminder("list\_select\_menu.xml", true);

mDbAdapter.createReminder("Custom layout for the root element", false);

}

}

码 ‑13 RemindersActivity

现在，当用户通过长按选择菜单项时，系统会调用 onCreateActionMode()
方法，并显示包含指定操作的关联操作栏。当关联操作栏可见时，用户可以选择其他菜单项。在某些情况下，如果关联操作提供常用的操作项，可能还需要通过添加复选框或类似的界面元素来支持用户选择菜单项，因为他们可能未发现长按行为。当用户选中该复选框时，可以使用
setItemChecked() 将相应的列表项设置为选中状态，从而调用关联操作模式。

![](md/media/chapter04_menus_and_app_bar/media/image7.png)

图 ‑7 ListView中启用批处理关联操作

1.  **弹出式菜单**

弹出式菜单（PopupMenu）是锚定在 View 中的模态菜单。如果空间足够，会显示在锚定视图下方，否则显示在其上方，它适用于：

  - 为与特定内容相关的操作提供溢出样式菜单。

注意：这与上下文菜单不同，后者通常用于影响所选内容的操作。对于影响所选内容的操作，使用关联操作模式或悬浮上下文菜单。

  - 提供命令语句的另一部分（例如，标记为“添加”且使用不同的“添加”选项生成弹出式菜单的按钮）。

  - 提供类似于 Spinner 且不保留永久选择的下拉菜单。

注意：PopupMenu 在 API 级别 11 及更高版本中可用。

如果使用 XML 定义菜单，弹出式菜单的显示方式如下：

  - 实例化 PopupMenu 及其构造函数，该函数接受当前应用的 Context 以及应锚定菜单的 View。

  - 使用 MenuInflater 将菜单资源膨胀到 PopupMenu.getMenu() 返回的 Menu 对象中。

  - 调用 PopupMenu.show()。

例如，以下是一个使用 android:onClick 属性显示弹出式菜单的按钮：

\<ImageButton

android:layout\_width="wrap\_content"

android:layout\_height="wrap\_content"

android:src="@drawable/ic\_overflow\_holo\_dark"

android:contentDescription="@string/descr\_overflow\_button"

android:onClick="showPopup" /\>

码 ‑14

稍后，活动可按如下方式显示弹出式菜单：

public void showPopup(View v) {

PopupMenu popup = new PopupMenu(this, v);

MenuInflater inflater = popup.getMenuInflater();

inflater.inflate(R.menu.actions, popup.getMenu());

popup.show();

}

码 ‑15

在 API 级别 14 及更高版本中，可以将两行合并在一起，使用
PopupMenu.inflate()展开菜单。当用户选择菜单项或轻触菜单以外的区域时，系统会关闭此菜单。可使用
PopupMenu.OnDismissListener 监听关闭事件。

如需在用户选择菜单项时执行操作，必须实现 PopupMenu.OnMenuItemClickListener 接口，并通过调用
setOnMenuItemclickListener() 将其注册到 PopupMenu。当用户选择菜单项时，系统会在接口中调用
onMenuItemClick() 回调，例如：

import android.os.Bundle;

import android.view.MenuItem;

import android.view.View;

import android.view.View.OnClickListener;

import android.widget.Button;

import android.widget.ImageView;

import android.widget.PopupMenu;

import android.widget.TextView;

import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.example.ch04.R;

public class PopupMenuActiviy extends AppCompatActivity {

/\*\* Called when the activity is first created. \*/

@Override

public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.popup\_menu);

Button button = (Button) findViewById(R.id.button);

TextView text = (TextView) findViewById(R.id.text);

ImageView image = (ImageView) findViewById(R.id.image);

button.setOnClickListener(viewClickListener);

text.setOnClickListener(viewClickListener);

image.setOnClickListener(viewClickListener);

}

OnClickListener viewClickListener = new OnClickListener() {

@Override

public void onClick(View v) {

// TODO Auto-generated method stub

showPopupMenu(v);

}

};

private void showPopupMenu(View v) {

PopupMenu popupMenu = new PopupMenu(PopupMenuActiviy.this, v);

popupMenu.getMenuInflater().inflate(R.menu.popupmenu,

popupMenu.getMenu());

popupMenu

.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {

@Override

public boolean onMenuItemClick(MenuItem item) {

Toast.makeText(PopupMenuActiviy.this, item.toString(),

Toast.LENGTH\_LONG).show();

return true;

}

});

popupMenu.show();

}

}

码 ‑16

![](md/media/chapter04_menus_and_app_bar/media/image8.png)

图 4‑8 弹出式菜单

### 菜单分组

菜单组是菜单项集合，可以用来为菜单项设置共同的属性。菜单组的设置可以使一组菜单选项的属性同时改变，呈现出共同的特性，例如：

  - 使用setGroupVisible()显示或隐藏组内所有选项。

  - 使用setGroupEnabled()启用或禁止组内所有选项。

  - 使用setGroupCheckable()说明组内所有的选项是否可选。

菜单组可以在菜单资源文件中定义，把\<item\>元素嵌套进\<group\>元素中来创建分组菜单；或者在安卓应用程序中，使用带有分组ID的add()方法创建分组。码
4‑17是一个在菜单资源文件中定义分组的简单例子。

\<?xml version="1.0" encoding="utf-8"?\>  

\<menu xmlns:android="http://schemas.android.com/apk/res/android"\>  

    \<item android:id="@+id/menu\_save"  

          android:icon="@drawable/menu\_save"  

          android:title="@string/menu\_save" /\>  

    \<\!-- menu group --\>  

    \<group android:id="@+id/group\_delete"\>  

        \<item android:id="@+id/menu\_archive"  

              android:title="@string/menu\_archive" /\>  

        \<item android:id="@+id/menu\_delete"  

              android:title="@string/menu\_delete" /\>  

    \</group\>  

\</menu\>  

码 ‑17 菜单分组定义

在码 4‑17中，分组菜单中的两个菜单项与第一个菜单项显示在同一个层次级别上，看上去没有什么区别。能够使用安卓
API中的方法，通过引用分组ID同时修改其中两个菜单项的属性，而且系统不会将分组的菜单项给分开。菜单组还可以用来显示应用程序中的选项开关，设置单选和多选两种方式（见图
4‑9）。

![](md/media/chapter04_menus_and_app_bar/media/image9.jpeg)

图 ‑9 单选模式菜单

如果菜单组中的菜单选项是图标类型，则不能显示成复选框或单选按钮。如果选择了让图标菜单中的菜单项可复选，就必须在每次状态改变时通过手动更换图标与文本来指明复选的状态。菜单资源文件中，\<item\>元素中的android:checkable属性用于给单独的菜单项定义是否可选，\<group\>元素中的android:checkableBehavior属性用于给一组菜单项定义可选类型（见码
4‑18）。

\<?xml version="1.0" encoding="utf-8"?\>  

\<menu xmlns:android="http://schemas.android.com/apk/res/android"\>  

    \<group android:checkableBehavior="single"\>  

        \<item android:id="@+id/red"  

              android:title="@string/red" /\>  

        \<item android:id="@+id/blue"  

              android:title="@string/blue" /\>  

    \</group\>  

\</menu\>  

码 ‑18为菜单项设置单选按钮

\<group\>元素的android:checkableBehavior属性可以有以下三种设置：

  - single代表菜单组中仅有一项能够被选（单选按钮）；

  - all代表所有菜单项都能够被选（复选框）；

  - none代表没有项目是可复选的。

在\<item\>元素中可以使用android:checked属性给菜单项设置默认的选择状态，也可以用setChecked()方法在代码中改变。当一个可复选的菜单项被选择的时候，系统会调用对应被选择的菜单项的回调方法（如onOptionsItemSelected()）。由于复选框或复选按钮不会自动的改变它们的状态，因此必须在这个方法中重新设置复选框的状态。一般使用isChecked()方法来查询复选菜单的当前状态（被用户选择之前的状态），然后用setChecked()方法设置选择状态（码
4‑19）。

@Override  

public boolean onOptionsItemSelected(MenuItem item) {  

    switch (item.getItemId()) {  

        case R.id.vibrate:  

        case R.id.dont\_vibrate:  

            if (item.isChecked()) item.setChecked(false);  

            else item.setChecked(true);  

            return true;  

        default:  

            return super.onOptionsItemSelected(item);  

    }  

}  

码 ‑19在事件处理方法中设置菜单项的选择状态

如果不用这种方式设置复选状态，那么当用户选择菜单项（复选框或复选按钮）的时候，它的可视状态将不会发生改变。

### 设置意图

菜单选项也可以创建意图来启动另一个活动，这个活动既可以是本应用程序中的，也可以是其它应用程序中的。如果确认了所需的意图的特性以及初始化此意图的菜单选项后，就可以在菜单选项事件响应的回调方法中使用startActivity()运行此意图。但是，添加调用这个意图对象的菜单项之后，如果不能确定用户设备上是否包含了处理这个意图对象的应用程序，就有可能由于没有接收这个意图对象的活动，导致这个菜单选项不会有任何作用，不能实现预期的功能，成为一个非功能性菜单选项。这个问题可以使用动态添加菜单项的方法来解决，安卓通过在设备上查找处理意图对象的活动，动态地把菜单项添加到菜单中。

为了防止上述问题发生，可以在添加菜单选项具体采取一些措施，例如：

  - 使用分类CATEGORY\_ALTERNATIVE 和CATEGORY\_SELECTED\_ALTERNATIVE定义的Intent。

  - 调用Menu.addIntentOptions()方法，安卓系统会搜索能够接收这个Intent对象的应用程序，将菜单选项添加到菜单中。

  - 如果没有应用程序满足Intent的要求，就不添加菜单选项。

由于CATEGORY\_SELECTED\_ALTERNATIVE只用于处理当前屏幕上被选择的元素，因此只在用onCreateContextMenu()方法创建菜单时使用这个分类（见码
4‑20）。

@Override

public boolean onCreateOptionsMenu(Menu menu){

    super.onCreateOptionsMenu(menu);

    // Create an Intent that describes the requirements to fulfill, to be included  

    // in our menu. The offering app must include a category value of Intent.CATEGORY\_ALTERNATIVE.

    Intent intent = new Intent(null, dataUri);

    intent.addCategory(Intent.CATEGORY\_ALTERNATIVE);

    // Search and populate the menu with acceptable offering applications.

    menu.addIntentOptions(

         R.id.intent\_group,  // Menu group to which new items will be added

         0,      // Unique item ID (none)

         0,      // Order for the items (none)

         this.getComponentName(),   // The current 活动 name

         null,   // Specific items to place first (none)

         intent, // Intent created above that describes our requirements

         0,      // Additional flags to control items (none)

         null);  // Array of MenuItems that correlate to specific items (none)

    return true;

}

码 ‑20动态添加Intent菜单选项

但凡发现活动提供的意图过滤器与定义的意图匹配，系统就会为相应活动添加菜单项，并使用意图过滤器 android:label
中的值作为菜单项标题，使用应用图标作为菜单项图标。addIntentOptions()
方法会返回已添加的菜单项数量。

## 应用栏

应用栏（ActionBar）是位于活动顶端的一个图形控件，能够显示活动的标题、图标、可能触发的动作、附加视图和其它交互控件，也可以用于在应用程序中导航。应用栏是用户浏览界面非常重要的设计元素，应用栏主要提供以下功能：

  - 支持应用程序内的一致导航和视图切换。

  - 为极少使用的操作提供“溢出动作”模式，减少混乱。

  - 提供专门位置来显示应用程序的标识，提示用户在当前应用程序中的位置

  - 突出重要操作，提供预访问模式

对于大多数应用，应用栏可以分割为应用图标、视图控制、动作按钮和溢出动作四个不同的功能区域，分别对应于**图
4‑10**中标识的“1”、“2”、“3”和“4”各区域。

![](md/media/chapter04_menus_and_app_bar/media/image10.png)

**图** **4‑10** 应用栏的各区域

  - 应用图标

应用图标是应用程序的标识，需要的话可以使用不同的。如果当前不是应用的顶层界面，则在图标左边会有一个向左的箭头，表示“向上”按钮，使用户可以回到上一级界面。

![](md/media/chapter04_menus_and_app_bar/media/image11.png)

**图** **4‑11 应用图标**

  - 视图控制

如果应用程序可以在多个不同的视图中显示数据，应用栏的视图控制能让用户在视图之间进行切换。例如可以在下拉菜单和标签视图之间进行切换。如果应用程序不支持不同的视图，也可以使用这个区域显示非交互内容，例如应用程序标题或较长的标识信息。

  - 动作按钮

用于显示应用程序中最重要的动作，安卓提供应用栏的设计模式是为了方便用户选择与上下文环境相关的最重要的动作，那些直接显示在动作按钮区域上的图标或者文本被称为动作按钮。如果应用栏的这部分区域不够容纳所有的动作，就自动移入后面的“溢出动作”。长点击图标就可以显示出“溢出动作”中隐藏的动作。

  - 溢出操作

平常很少用到的动作一般被放在这部分区域中。动作按钮区域和溢出动作区域中的动作按钮可能会根据屏幕的大小和形状发生变化。

安卓 2.3以及之前的系统使用设备上的返回键实现一个应用程序内部的浏览，这就是返回浏览模式。但是从安卓
3.0版本开始，安卓设备使用虚拟浏览条，即应用栏，代替了传统的物理按键。从安卓
3.0 (API level
11)开始，当活动使用系统的默认主题模式显示时，应用栏就出现在活动窗口的顶部。当然也可以通过属性设定应用栏的模式和增加应用栏。应用栏首先在安卓
3.0 (API level 11)系统使用 ，但是要在安卓 2.1 (API level 7)
的版本中运行时，也可以导入相应的库兼容支持。在不同的安卓版本中，大多数API都是相同的，只是所在的包不同。

  - 如果支持API level 11低的版本：import android.support.v7.app.ActionBar

  - 如果支持API level 11高的版本：import android.app.ActionBar

默认状态下，应用栏跟在应用程序标题后面，显示在应用程序的顶部。如果活动设置了弹出菜单，就可以直接从应用栏以动作选项的方式直接访问。使用应用栏可以与许多控件结合使用，应用栏支持的基本应用模式包括：项目、视图、动作提供器、导航标签、下拉菜单。

### 添加项目

从安卓3.0（API Level
11）开始，应用栏就被包含在使用Theme.Hole主题的活动中，或者是这些活动的子类中。应用栏只能运行在安卓3.0（API
Level
11）或更高的版本上，应用栏替代了选项菜单，并且替换了传统的应用程序标题栏。在添加项目到应用栏之前，因此需要打开Manifest文件，确认用户SDK的属性targetSdkVersion或minSdkVersion属性被设置为11或更大的数值。下面的代码是一个设置targetSdkVersion属性的例子。

\<manifest ... \>

    \<uses-sdk android:minSdkVersion="4"

              android:targetSdkVersion="11" /\>

    ...

\</manifest\>

码 ‑21

在这个例子中，应用程序运行要求的最小API版本是4（安卓 1.6），但是目标API版本是11（安卓 3.0），在安卓
1.6版本支持下，应用程序可以完成基础重要的功能，但当应用程序运行在安卓3.0或更高的版本上时，系统就会给每个活动应用全景主题，支持所有设计的界面和功能，包括应用栏。如果要使用应用栏API来进行添加导航模式和修改应用栏样式的操作，就要把minSdkVersion属性设置为11或更大的值。在察看API版本的同时，还需要确认下面列出的代码不存在，否则应用程序运行时应用栏也不会显示。

\<application android:theme="@android:style/Theme.NoTitleBar"

码 ‑22

若在应用程序界面中不需要应用栏，可把活动的主题设置为Theme.Holo.NoActionBar就可以了。具体代码如下：

\<activity android:theme="@android:style/Theme.Holo.NoActionBar"\>

码 ‑23

应用栏的显示和隐藏也可以在应用程序运行中动态调整。在活动中使用getActionBar()获取应用栏对象后，使用ActionBar提供的hide()方法和show()方法可以直接改变应用栏的显示状态，代码如下。

ActionBar actionBar =
[getActionBar()](http://developer.android.com/reference/android/app/Activity.html#getActionBar\(\));

actionBar.hide();

码 ‑24

当应用栏隐藏时，系统会调整活动的显示大小，来填充当前有效的屏幕空间。在隐藏和删除应用栏时，为了填充被应用栏占用的空间，可能会导致的活动的重新布局。如果的活动有规律的隐藏和显示应用栏，使用覆盖模式是较好的选择。设置应用栏覆盖模式，需要给活动创建一个主题，并且把android:windowActionBarOverlay属性设置为true。缺省情况下，系统在应用栏中使用应用的图标，这是通过\<application\>和\<activity\>元素中的icon属性指定。如果设置了logo属性，应用栏会使用logo属性指定的图片资源，而代替icon属性指定的资源。由于安卓
3.0及以上版本用应用栏替代了选项菜单，当活动首次启动时，系统会调用onCreateOptionsMenu()方法创建Action
Items类型的应用栏，可以在这个方法中获取定义好的XML菜单资源。而且当Action
Items的某个选项被选中时，对用户操作事件的获取和处理，安卓系统同样调用onOptionsItemSelected()方法来进行处理。因此应用栏中Action
Items的定义与菜单选项相同，创建Action
Items类型应用栏和事件处理的过程也基本与选项菜单的步骤一致。下面我们使用一个简单的例子，来说明一下在应用程序中如何实现Action
Items的应用栏。假设活动的布局文件main\_activity\_actions.xml已经存在：

（1）确认Manifest文件中的设置

（2）打开或创建一个活动

（3）若菜单资源目录/res/menu不存在，创建这个目录

（4）创建菜单文件

在/res/menu目录下创建菜单资源文件menu\_actions.xml，在其中设置三个菜单选项。

\<menu xmlns:android="http://schemas.android.com/apk/res/android" \>

    \<item android:id="@+id/action\_search"

          android:icon="@drawable/ic\_action\_search"

          android:title="@string/action\_search"/\>

    \<item android:id="@+id/action\_compose"

          android:icon="@drawable/ic\_action\_save"

          android:title="@string/action\_save" /\>

    \<item android:id="@+id/itemHelp"

          android:icon="@drawable/ic\_action\_help"

android:title="@string/btnHelp" /\>

\</menu\>

码 ‑25应用栏项目的定义

（5）设置显示动作选项

在XML文件中，能够通过设置\<item\>元素的android:showAsAction=”ifRoom”属性，使菜单项显示在应用栏中。菜单项首先作为动作按钮显示在应用栏中，如果没有足够的空间，其他菜单项会显示在溢出动作中。\<item\>元素的android:showAsAction有一系列属性，下面列出重要的几个：

  - never：此菜单项不显示在应用栏中，当点击菜单按钮时，显示在列表中。

  - ifRoom：如果应用栏有足够空间，此菜单项显示在应用栏上。

  - always：此菜单项一直作为动作按钮显示，但不推荐使用，可能会引起视图重叠。

  - withText：此菜单项使用定义的文本模式显示。

如果的菜单项使用android:title和android:icon属性同时设置了标题和图标，那么默认情况下应用栏中动作项仅显示图标。如果要显示文本标题，就要给android:showAsAction属性添加withText设置，表示需要显示标题，代码如下：

android:showAsAction="ifRoom|withText" /\>

码 ‑26

一般来说，即使不需要在应用栏上显示文本标题，也应该在XML文件中给每个菜单项定义android:title属性，这是因为需要考虑下面几种情况：

  - 如果应用栏中没有足够的空间来显示动作项，菜单项就会显示在溢出动作中，在这里只能显示标题。

  - 若只用图标来显示动作项，当用户长点击动作按钮时，可以显示带有标题的提示信息。

  - 屏幕阅读器可以给有视觉障碍的用户朗读菜单项标题。

添加动作选项显示设置代码后的菜单资源资源文件见码 4‑27。

\<menu xmlns:android="http://schemas.android.com/apk/res/android" \>

    \<item android:id="@+id/action\_search"

          android:icon="@drawable/ic\_action\_search"

          android:showAsAction="ifRoom|withText" /\>

          android:title="@string/action\_search"/\>

    \<item android:id="@+id/action\_compose"

          android:icon="@drawable/ic\_action\_save"

          android:showAsAction="ifRoom|withText" /\>

          android:title="@string/action\_save" /\>

    \<item android:id="@+id/itemHelp"

          android:icon="@drawable/ic\_action\_help"

         android:showAsAction="ifRoom|withText" /\>

         android:title="@string/btnHelp" /\>

\</menu\>

码 ‑27设置显示动作选项

（6）获取Menu XML定义的动作选项资源

在活动中，动作选项资源的获取方式与选项菜单相同，都可以从预定义好的菜单资源文件中加载，在onCreateOptionsMenu()方法中实现创建动作选项。使用MenuInflater类的inflate()方法，从menu\_actions.xml（码
4‑27）文件中获取预定义的菜单项资源，作为动作选项（见码 4‑28）。

@Override

public boolean onCreateOptionsMenu(Menu menu) {

    // Inflate the menu items for use in the action bar

    MenuInflater inflater = getMenuInflater();

    inflater.inflate(R.menu.menu\_actions, menu);

    return super.onCreateOptionsMenu(menu);

}

码 ‑28获取菜单资源

（7）响应项目选择事件

动作选项加载后，需要完成的就是动作选项的事件处理了。与选项菜单相同，最简单的方法就是使用活动提供的onOptionsItemSelected()方法。当用户选择了一个动作时，系统会调用活动的onOptionsItemSelected()方法，并且传递MenuItem对象给这个方法。可以使用getItemId()方法得到android:id属性为菜单项定义的ID，用来识别用户点击的动作。

@Override

public boolean onOptionsItemSelected(MenuItem item) {

    // Handle presses on the action bar items

    switch (item.getItemId()) {

        case R.id.action\_search:

            Toast.makeText(MainActivity.this, "Search is Selected",
Toast.LENGTH\_SHORT).show();

            return true;

        case R.id.action\_save:

            Toast.makeText(MainActivity.this, "Save is Selected",
Toast.LENGTH\_SHORT).show();

              return true;

        case R.id.action\_help:

             Toast.makeText(MainActivity.this, "Help is Selected",
Toast.LENGTH\_SHORT).show();

             return true;

        default:

            return super.onOptionsItemSelected(item);

    }

}

码 ‑29事件处理

如果在片段中添加菜单项，那么通过片段类的onCreateOptionsMenu回调方法，当用户选择其中一个片段的菜单项时，系统会调用那个片段对象对应的onOptionsItemSelected()方法。

### 添加视图

动作视图是作为动作按钮的替代品显示在应用栏中的一个可视构件。动作视图在不改变活动或片段的情况下，可以给用户提供快捷的访问和丰富的操作。例如，如果有一个用于搜索的可选菜单项![](md/media/chapter04_menus_and_app_bar/media/image12.png)，可以用SearchView类来替代应用栏上的搜索按钮![](md/media/chapter04_menus_and_app_bar/media/image12.png)。**图
4‑12**显示了从动作按钮![](md/media/chapter04_menus_and_app_bar/media/image12.png)（上图）展开为动作视图的例子。

![](md/media/chapter04_menus_and_app_bar/media/image13.png)

**图** **4‑12 动作视图操作**

如需添加操作视图，请按照添加操作按钮中所述，在工具栏的菜单资源中创建 \<item\> 元素。请将以下两个属性中的一个添加到 \<item\>
元素：

  - actionViewClass：实现操作的控件类。

  - actionLayout：描述操作组件的布局资源。

将showAsAction属性设为 "ifRoom|collapseActionView" 或
"never|collapseActionView"。collapseActionView
标记用于指明在用户未与微件互动时应如何显示微件：如果微件位于应用栏中，则应用应该将微件显示为图标。如果微件位于溢出菜单中，则应用应该将微件显示为菜单项。当用户与操作视图互动时，它应该展开以填满应用栏，例如码
4‑30菜单资源文件中指定了一个SearchView控件作为动作视图。

\<menu xmlns:app="http://schemas.android.com/apk/res-auto"

xmlns:android="http://schemas.android.com/apk/res/android" \>

\<item

android:id="@+id/action\_search"

android:orderInCategory="100"

android:icon="@drawable/ic\_launcher\_background"

android:title="@string/action\_search"

app:showAsAction="always"

app:actionViewClass="androidx.appcompat.widget.SearchView" /\>

\</menu\>

码 ‑30在菜单资源中指定搜索视图

app:showAsAction属性也可包含“collapseActionView”属性值，这个值是可选的。如果app:showAsAction属性得值包含了“collapseActionView”，说明所声明的动作视图会折叠到一个按钮中，当用户选择这个按钮时再展开，默认状态下这个动作视图是可见的，要占据应用栏的有效空间。如果为某个动作选项设定了动作视图，由于在用户选择这个动作选项时，系统会展开对应的动作视图，因此不必在onOptionsItemSelected()回调方法中响应这个动作选项，进行相应的事件处理，而是直接交给动作视图的事件处理代码来完成。但是，如果onOptionsItemSelected()中设置返回true值，系统还是会回调这个方法，动作视图则不会打开。当用户选择了应用栏中的“向上”图标或按下了回退按钮时，系统也会把动作视图折叠起来。如果需要，可以在代码中通过expandActionView()和collapseActionView()方法来展开或折叠动作视图。

动作视图的事件处理代码放在活动的onCreateOptionsMenu()方法内，当事件发生时系统回调此方法，由注册的监听器捕获相应的事件，执行事件处理代码，实现选择动作视图后对应的功能。在onCreateOptionsMenu()方法中，通过调用带有菜单选项ID的findItem()方法来获取菜单选项，然后再调用getActionView()方法获得动作视图中的元素（码
4‑31）。在对动作视图操作时，会有两种情况：展开和折叠。针对这两种操作，安卓提供了OnActionExpandListener()接口的onMenuItemActionExpand方法和onMenuItemActionCollapse方法，在事件发生时进行回调。编写事件处理代码时，可以首先实现OnActionExpandListener接口，根据不同的操作，分别在对应的方法中实现相应的功能。然后创建一个OnActionExpandListener对象，即一个事件，并使用setOnActionExpandListener()方法来注册这个事件。完成这些工作，系统就能够在动作视图展开和折叠时使用相应的回调方法进行处理了（见码
4‑31）。

import android.app.SearchManager;

import android.content.Context;

import android.os.Bundle;

import android.view.Menu;

import android.widget.ListView;

import androidx.appcompat.app.AppCompatActivity;

import androidx.appcompat.widget.SearchView;

import com.example.ch04.R;

import java.util.ArrayList;

public class SearchViewActivity extends AppCompatActivity implements
SearchView.OnQueryTextListener {

// Declare Variables

ListView list;

ListViewAdapter adapter;

String\[\] animalNameList;

ArrayList\<AnimalNames\> arraylist = new ArrayList\<AnimalNames\>();

@Override

public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.search\_view\_main);

// Generate sample data

animalNameList = new String\[\]{"Lion", "Tiger", "Dog",

"Cat", "Tortoise", "Rat", "Elephant", "Fox",

"Cow", "Donkey", "Monkey"};

// Locate the ListView in listview\_main.xml

list = (ListView) findViewById(R.id.listview);

for (int i = 0; i \< animalNameList.length; i++) {

AnimalNames animalNames = new AnimalNames(animalNameList\[i\]);

// Binds all strings into an array

arraylist.add(animalNames);

}

// Pass results to ListViewAdapter Class

adapter = new ListViewAdapter(this, arraylist);

// Binds the Adapter to the ListView

list.setAdapter(adapter);

}

@Override

public boolean onCreateOptionsMenu(Menu menu) {

getMenuInflater().inflate(R.menu.main, menu);

// Get the SearchView and set the searchable configuration

SearchManager searchManager = (SearchManager)
getSystemService(Context.SEARCH\_SERVICE);

SearchView searchView = (SearchView) menu.findItem(R.id.action\_search)

.getActionView();

searchView.setSearchableInfo(searchManager

.getSearchableInfo(getComponentName()));

searchView.setIconifiedByDefault(false);

searchView.setOnQueryTextListener(this);

return true;

}

@Override

public boolean onQueryTextSubmit(String query) {

return false;

}

@Override

public boolean onQueryTextChange(String newText) {

String text = newText;

adapter.filter(text);

return false;

}

}

码 ‑31 SearchViewActivity

![](md/media/chapter04_menus_and_app_bar/media/image14.png)

图 ‑13 SearchViewActivity

### 动作提供器

与动作视图类似，动作提供器Action
Provider使用一个定制的布局代替一个动作按钮。与动作视图不同，当点击动作提供器时，其可以显示子菜单，并需对所有选项进行控制处理。有两种方式来使用Action
Provider

  - 在应用程序代码中，调用MenuItem类提供的方法setActionProvider(ActionProvider)，直接在菜单选项上设置动作提供器。

  - 在菜单资源文件中声明动作提供器。

安卓系统提供了一些预定义的Action
Provider，例如，ShareActionProvider提供了一个子菜单，菜单中包括“Google+”、“Hangouts”和“Messaging”等菜单选项，已经实现了其功能。通过使用这个预定义的动作提供器，可以将这些可能共享的应用列表直接在应用栏上显示出来，供用户使用。使用如果要声明一个动作提供器，需在菜单资源文件中，设定所对应的\<item\>元素的android:actionProviderClass属性，使用所选的预定义Action
Provider的完整类名来指定动作提供器（码
4‑32）。如果需要创建自己定义的动作提供其，也可以通过继承ActionProvider类来定义。

\<?xml version="1.0" encoding="utf-8"?\>

\<menu xmlns:android="http://schemas.android.com/apk/res/android"\>

\<item android:id="@+id/menu\_share"

          android:title="@string/share"

          android:showAsAction="ifRoom"

         
android:actionProviderClass="android.widget.ShareActionProvider" /\>

  ...

\</menu\>

码 ‑32声明动作提供器

由于每一个ActionProvider类，定义自身的动作行为，就不必在 onOptionsItemSelected()监听动作事件了。如果需要在
onOptionsItemSelected()方法中对其他的动作的事件进行处理，必须确认此方法的返回值为false，从而保证动作提供器上菜单选项的事件发生时，系统能够回调onPerformDefaultAction()方法，执行其事件处理代码。前面的知识中提到过，在用户界面上选择选项菜单的任何一个选项，系统都会回调onOptionsItemSelected()方法，而应用栏是安卓高级版本中选项菜单的替代控件，所以一般情况下，可以把动作提供器的事件处理也放在onOptionsItemSelected()方法中，系统会在选择动作提供器时，回调这个方法。值得注意的是，如果动作提供器定义了一个子菜单，则在用户打开子菜单列表或选择子菜单选项时，活动无法接收onOptionsItemSelected()的回调，事件处理代码必须写在onPerformDefaultAction()方法中。

#### 系统定义

安卓系统定义了一些动作提供器，提供了丰富的功能。每个ActionProvider类都定义了自己布局（例如子菜单）和动作行为，其他的应用程序在使用这些ActionProvider时，可以直接使用其功能，由onPerformDefaultAction()回调方法中系统定义的代码来处理事件，不需要再在onOptionsItemSelected()方法中监听动作和进行事件处理。但是，尽管预定义的ActionProvider提供了其在溢出菜单中所能执行的默认操作，应用程序的
活动（或片段）也能够通过处理来自onOptionsItemSelected()回调方法的点击事件，来重写这个默认操作。系统预定义ActionProvider之一的
ShareActionProvider类作为例子，说明如何使用这些ActionProvider。ShareActionProvider类提供共享动作，负责创建子菜单的所有逻辑，包括共享视图的封装、点击事件的处理以及溢出菜单中的选项显示等，在使用这个类时，所需要编写的唯一的代码就是给对应的菜单项声明动作提供器，并指定共享的Intent对象。如果要在应用栏中提供一个“共享”动作，来充分利用安装在设备上的其他应用程序，例如把一张图片共享给消息或社交应用程序使用，则使用ShareActionProvider类是一个有效的方法。要使用ShareActionProvider实现共享动作，主要完成下面几个工作：

  - 定义菜单资源文件

在\<item\>元素下，定义actionProviderClass属性的值为ShareActionProvider的完整类名。

\<?xml version="1.0" encoding="utf-8"?\>

\<menu xmlns:android="http://schemas.android.com/apk/res/android"

xmlns:support="http://schemas.android.com/apk/res-auto"\>

\<item

android:id="@+id/menu\_share"

android:title="@string/share\_provider"

support:actionProviderClass="androidx.appcompat.widget.ShareActionProvider"

support:showAsAction="always" /\>

\</menu\>

码 ‑33

  - 获取ActionProvider对象

在活动中定义一个
ShareActionProvider类型的私有变量mShareActionProvider，然后在onCreateOptionsMenu()方法中，使用getActionProvider()方法获取跟menu\_share菜单项匹配的ShareActionProvider对象，调用setShareIntent()方法的是定义要用于共享的Intent对象。码
4‑33中定义了getDefaultIntent()方法初始化Action
Provider，但是当在Intent中实际使用的内容设定或改变时，必须调用mShareActionProvider.setShareIntent()更新共享Intent。

public class ShareProviderActivity extends AppCompatActivity {

// The items to be displayed in the ViewPager

private final ArrayList\<ContentItem\> mItems = getSampleContent();

protected void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

// Set content view (which contains a CheeseListFragment)

setContentView(R.layout.share\_provider);

// Retrieve the ViewPager from the content view

ViewPager vp = (ViewPager) findViewById(R.id.viewpager);

// Set an OnPageChangeListener so we are notified when a new item is
selected

vp.addOnPageChangeListener(mOnPageChangeListener);

// Finally set the adapter so the ViewPager can display items

vp.setAdapter(mPagerAdapter);

}

// BEGIN\_INCLUDE(get\_sap)

@Override

public boolean onCreateOptionsMenu(Menu menu) {

// Inflate the menu resource

getMenuInflater().inflate(R.menu.shareprovder\_menu, menu);

// Retrieve the share menu item

MenuItem shareItem = menu.findItem(R.id.menu\_share);

// Now get the ShareActionProvider from the item

mShareActionProvider = (ShareActionProvider)
MenuItemCompat.getActionProvider(shareItem);

// Get the ViewPager's current item position and set its ShareIntent.

int currentViewPagerItem = ((ViewPager)
findViewById(R.id.viewpager)).getCurrentItem();

setShareIntent(currentViewPagerItem);

return super.onCreateOptionsMenu(menu);

}

private void setShareIntent(int position) {

// BEGIN\_INCLUDE(update\_sap)

if (mShareActionProvider \!= null) {

// Get the currently selected item, and retrieve it's share intent

ContentItem item = mItems.get(position);

Intent shareIntent = item.getShareIntent(ShareProviderActivity.this);

// Now update the ShareActionProvider with the new share intent

mShareActionProvider.setShareIntent(shareIntent);

}

// END\_INCLUDE(update\_sap)

}

}

码 ‑34使用ShareActionProvider

码
4‑33中ShareActionProvider对象处理所有的跟这个菜单项有关的用户交互，并且不需要处理来自onOptionsItemSelected()回调方法的点击事件。如果需要重新定义菜单项的功能，可以重写onOptionsItemSelected()中对应的处理代码。码
4‑34中在
onOptionsItemSelected()回调方法中重写了menu\_share菜单项的点击事件处理代码，因此当用户点击这个菜单项时，系统不再执行原有的菜单功能，而是实现重写后的代码。

@Override

public boolean onCreateOptionsMenu(Menu menu) {

getMenuInflater().inflate(R.menu.activity\_main, menu);

// Get the ActionProvider for later usage

provider = (ShareActionProvider) menu.findItem(R.id.menu\_share)

.getActionProvider();

return true;

}

@Override

public boolean onOptionsItemSelected(MenuItem item) {

switch (item.getItemId()) {

case R.id.menu\_share:

doShare();

break;

default:

break;

}

return true;

}

public void doShare() {

// populate the share intent with data

Intent intent = new Intent(Intent.ACTION\_SEND);

intent.setType("text/plain");

intent.putExtra(Intent.EXTRA\_TEXT, "This is a message for you");

provider.setShareIntent(intent);

}

码 ‑35重写onOptionsItemSelected()中对应的处理代码

默认情况，ShareActionProvider对象会基于用户的使用频率来保留共享目标的排列顺序。使用频率高的目标应用程序会显示在下拉列表的上面，并且最常用的目标会作为默认共享目标直接显示在应用栏。默认情况下，排序信息被保存在由DEFAULT\_SHARE\_HISTORY\_FILE\_NAME指定名称的私有文件中。如果只使用一种操作类型ShareActionProvider类或它的一个子类，可以继续使用这个默认的历史文件；但是，如果使用了不同类型的多个操作的ShareActionProvider类或它的子类，则需要调用setShareHistoryFileName()方法，为每种ShareActionProvider类都指定自己独立的XML历史文件。

#### 自定义

要创建自己的动作提供器，只需继承类，并且实现合适的回调方法。下面是需要实现的几个重要回调方法：

  - ActionProvider()

构造方法负责传递应用程序的Context对象，这个对象需要保存在一个成员变量中，以便其他的回调方法使用。

  - OnCreateActionView()

在这个方法中，可以给菜单项定义动作视图。使用从构造方法中接收的Context对象，实例化一个LayoutInflater对象，并且用XML资源来填充动作视图，然后注册事件监听器，见码
4‑35。

public View onCreateActionView() {

    // Inflate the action view to be shown on the action bar.

    LayoutInflater layoutInflater = LayoutInflater.from(mContext);

    View view = layoutInflater.inflate(R.layout.action\_provider, null);

    ImageButton button = (ImageButton) view.findViewById(R.id.button);

    button.setOnClickListener(new View.OnClickListener() {

        @Override

        public void onClick(View v) {

            // Do something...

        }

    });

    return view;

}

码 ‑36创建自己的ActionProvider动作视图

  - onPerformDefaultAction()

系统会在选中溢出菜单中的菜单选项时，调用这个方法，并且动作提供器执行这个选中菜单项执行的默认操作。如果自定义的动作提供器，使用onPrepareSubMenu()回调方法创建了子菜单，即使把这个动作提供器放在溢出菜单中，子菜单也会显示。因此，当自定义动作提供器中子菜单存在时，系统不会回调onPerformDefaultAction()方法。

##  小结

本章主要介绍了安卓系统的应用浏览模式中菜单和应用栏两个最重要的组件。安卓
SDK支持丰富的菜单类型，包括常规的菜单，子菜单，上下文菜单，图标菜单，二级菜单和替代菜单。其中选项菜单、弹出菜单和上下文菜单是菜单的三个基本类型。菜单项由android.view.MenuItem类表示，子菜单由android.view.SubMenu类表示。菜单的创建和事件处理与其他视图对象类似，可以通过XML布局文件设计菜单，使用监听器或菜单的回调方法来处理菜单选项事件。应用栏是位于活动顶端的一个图形控件，能够显示活动的标题、图标、可能触发的动作、附加视图和其它交互控件，也可以用于在应用程序中导航。对于大多数应用，应用栏可以分割为应用图标、视图控制、动作按钮和溢出动作四个不同的功能区域。应用栏可以与许多控件结合使用，例如菜单、视图、标签和内容提供器等组件，为安卓用户界面提供了丰富的显示模式。
