# 界面设计基础

每个安卓客户端应用首先面对的就是界面的开发。安卓系统提供了丰富界面控件。安卓提供的用户图形交互界面称为活动。本章主要介绍活动的基本知识和使用方法，如何在活动上实现不同的布局，如何理解字符串、图片等资源的使用。

## 开发基础知识

可以使用 Kotlin、Java 和 C++ 语言编写安卓应用。Android SDK 工具会将代码连同任何数据和资源文件编译成一个
APK（Android 软件包），即带有 .apk 后缀的归档文件。一个 APK
文件包含安卓应用的所有内容，其也是安卓设备用来安装应用的文件。每个安卓应用都处于各自的安全沙盒中，并受以下安卓安全功能的保护：

  - 安卓操作系统是一种多用户 Linux 系统，其中的每个应用都是一个不同的用户；

  - 默认情况下，系统会为每个应用分配一个唯一的 Linux 用户 ID（该 ID
    仅由系统使用，应用并不知晓）。系统会为应用中的所有文件设置权限，使得只有分配给该应用的用户
    ID 才能访问这些文件；

  - 每个进程都拥有自己的虚拟机 (VM)，因此应用代码独立于其他应用而运行。

  - 默认情况下，每个应用都在其自己的 Linux
    进程内运行。安卓系统会在需要执行任何应用组件时启动该进程，然后当不再需要该进程或系统必须为其他应用恢复内存时，其便会关闭该进程。

安卓统实现了最小权限原则。换言之，默认情况下每个应用只能访问执行其工作所需的组件，而不能访问其他组件。这样便能创建非常安全的环境，在此环境中应用无法访问其未获得权限的系统部分。应用仍可通过一些途径与其他应用共享数据以及访问系统服务：

  - 可以安排两个应用共享同一Linux用户ID，在此情况下，二者便能访问彼此的文件。为节省系统资源，也可安排拥有相同用户ID的应用在同一Linux进程中运行，并共享同一虚拟机。应用还必须使用相同的证书进行签名。

  - 应用可以请求访问设备数据（如用户的联系人、短信消息、可装载存储装置（SD 卡）、相机、蓝牙等）的权限。用户必须明确授予这些权限。

本节其余部分将介绍以下概念：

  - 用于定义应用的核心框架组件

  - 用来声明组件和应用必需设备功能的清单文件。

  - 与应用代码分离并允许应用针对各种设备配置适当优化其行为的资源。

### 应用组件

应用组件是安卓应用的基本构建块。每个组件都是一个入口点，系统或用户可通过该入口点进入应用，有些组件会依赖于其他组件。共有四种不同的应用组件类型：

  - 活动（Activity）

  - 服务（Service）

  - 广播接收器（BroadcastRecerive）

  - 内容提供程序(ContentProvider)

每种类型都有不同的用途和生命周期，后者会定义如何创建和销毁组件。以下部分将介绍应用组件的四种类型。

  - 活动

活动是与用户交互的入口点。其表示拥有界面的单个屏幕。例如，电子邮件应用可能有一个显示新电子邮件列表的活动、一个用于撰写电子邮件的活动以及一个用于阅读电子邮件的活动。尽管这些活动通过协作在电子邮件应用中形成一种紧密结合的用户体验，但每个活动都独立于其他活动而存在。因此，其他应用可以启动其中任何一个活动（如果电子邮件应用允许）。例如，相机应用可以启动电子邮件应用内用于撰写新电子邮件的活动，以便用户共享图片。活动有助于完成系统和应用程序之间的以下重要交互：

  - 系统追踪用户当前关心的内容（例如，屏幕上显示的内容），以确保系统继续运行托管活动的进程。

  - 系统了解先前使用的进程包含用户可能返回的内容（例如，已停止的活动），从而更优先保留这些进程。

  - 系统帮助应用处理终止其进程的情况，以便用户可以返回已恢复其先前状态的活动。

  - 提供一种途径，让应用实现彼此之间的用户流，并让系统协调这些用户流，此处最经典的示例是共享。

<!-- end list -->

  - 服务

服务是一个通用入口点，用于因各种原因使应用在后台保持运行状态。其是一种在后台运行的组件，用于执行长时间运行的操作或为远程进程执行作业。服务不提供界面，例如当用户使用其他应用时，服务可能会在后台播放音乐或通过网络获取数据，但这不会阻断用户与活动的交互。诸如活动等其他组件可以启动服务，使该服务运行或绑定到该服务，以便与其进行交互。事实上，有两种截然不同的语义服务可以告知系统如何管理应用：已启动服务会告知系统使其运行至工作完毕，此类工作可以是在后台同步一些数据；或者在用户离开应用后继续播放音乐。在后台同步数据或播放音乐也代表了两种不同类型的已启动服务，而这些服务可以修改系统处理方式：

  - 音乐播放是用户可直接感知的服务，因此应用会向用户发送通知，表明其希望成为前台，从而告诉系统此消息；在此情况下，系统明白其应尽全力维持该服务进程运行，因为进程消失会令用户感到不快。

  - 通常用户不会意识到常规后台服务正处于运行状态，因此系统可以更自由地管理其进程。如果系统需要使用内存来处理用户更迫切关注的内容，则其可能允许终止服务，然后在稍后的某个时刻重启服务。

绑定服务之所以能运行，原因是某些其他应用或系统已表示希望使用该服务。本质上说，这是为另一个进程提供 API
的服务。因此，系统会知晓这些进程之间存在依赖关系，所以如果进程 A
绑定到进程 B 中的服务，系统便知道自己需使进程 B（及其服务）为进程 A 保持运行状态。此外，如果进程 A
是用户关心的内容，系统随即也知道将进程 B
视为用户关心的内容。由于存在灵活性，服务已成为非常有用的构建块，并且可实现各种高级系统概念。动态壁纸、通知侦听器、屏幕保护程序、输入方法、无障碍功能服务以及众多其他核心系统功能均可构建为在其运行时由应用实现、系统绑定的服务。

如果应用面向 Android 5.0（API 级别 21）或更高版本，使用 JobScheduler 类来调度操作。JobScheduler
的优势在于，其能通过优化作业调度来降低功耗，以及使用 Doze API，从而达到省电目的。

  - 广播接收器

借助广播接收器组件，系统能够在常规用户流之外向应用传递事件，从而允许应用响应系统范围内的广播通知。由于广播接收器是另一个明确定义的应用入口，因此系统甚至可以向当前未运行的应用传递广播。例如，应用可通过调度提醒来发布通知，以告知用户即将发生的事件，而且通过将该提醒传递给应用的广播接收器，应用在提醒响起之前即无需继续运行。许多广播均由系统发起，例如通知屏幕已关闭、电池电量不足或已拍摄照片的广播。应用也可发起广播，例如通知其他应用某些数据已下载至设备，并且可供其使用。尽管广播接收器不会显示界面，但其可以创建状态栏通知，在发生广播事件时提醒用户。但广播接收器更常见的用途只是作为通向其他组件的通道，旨在执行极少量的工作，例如其可能会根据带
JobScheduler 的事件调度 JobService 来执行某项工作广播接收器作为 BroadcastReceiver
的子类实现，并且每条广播都作为意图（Intent）对象进行传递。

  - 内容提供程序

内容提供程序管理一组共享的应用数据，可以将这些数据存储在文件系统、SQLite
数据库、网络中或者应用可访问的任何其他持久化存储位置。其他应用可通过内容提供程序查询或修改数据（如果内容提供程序允许），例如安卓系统可提供管理用户联系人信息的内容提供程序，因此任何拥有适当权限的应用均可查询内容提供程序（如
ContactsContract.Data），以读取和写入特定人员的相关信息。很容易将内容提供程序看作数据库上的抽象，因为其内置的大量 API
和支持时常适用于这一情况，但从系统设计的角度看，二者的核心目的不同。对系统而言，内容提供程序是应用的入口点，用于发布由 URI
架构识别的已命名数据项，因此应用可以决定如何将其包含的数据映射到 URI 命名空间，进而将这些 URI
分发给其他实体，反之这些实体也可使用分发的 URI 来访问数据。在管理应用的过程中，系统可以执行以下特殊操作：

  - 分配 URI 无需应用保持运行状态，因此 URI 可在其所属的应用退出后继续保留。当系统必须从相应的 URI
    检索应用数据时，系统只需确保所属应用仍处于运行状态。

  - 这些 URI 还会提供重要的细粒度安全模型，例如应用可将其所拥有图像的 URI
    放到剪贴板上，但将其内容提供程序锁定，以便其他应用程序无法随意访问其。当第二个应用尝试访问剪贴板上的
    URI 时，系统可允许该应用通过临时的 URI 授权来访问数据，这样便只能访问 URI 后面的数据，而非第二个应用中的其他任何内容。

内容提供程序也适用于读取和写入应用不共享的私有数据。内容提供程序作为 ContentProvider 的子类实现，并且其必须实现一组标准
API，以便其他应用能够执行事务。

安卓系统设计的独特之处在于，任何应用都可启动其他应用的组件。例如，当想让用户使用设备相机拍摄照片时，另一个应用可能也可执行该操作，因而应用便可使用该应用，而非自行产生一个活动来拍摄照片。无需加入甚至链接到该相机应用的代码。只需启动拍摄照片的相机应用中的活动即可。完成拍摄时，系统甚至会将照片返回应用，以便您使用。对用户而言，这就如同相机是您应用的一部分。

当系统启动某个组件时，其会启动该应用的进程（如果尚未运行），并实例化该组件所需的类。例如，如果应用启动相机应用中拍摄照片的活动，则该活动会在属于相机应用的进程（而非应用进程）中运行。因此，与大多数其他系统上的应用不同，安卓应用并没有单个入口点（即没有
main() 函数）。

由于系统在单独的进程中运行每个应用，且其文件权限会限制对其他应用的访问，因此应用无法直接启动其他应用中的组件，但安卓系统可以。如要启动其他应用中的组件，请向系统传递一条消息，说明启动特定组件的意图，系统随后便会启动该组件。

### 启动组件

在四种组件类型中，有三种包括活动、服务和广播接收器，均通过异步消息意图进行启动。意图会在运行时对各个组件进行互相绑定。无论该组件是属于自身应用还是其他应用，可以将意图视为从其他组件请求操作的信使。需使用
Intent对象创建意图，该对象通过定义消息来启动特定组件（显式意图）或特定的组件类型（隐式意图）。

对于活动和服务，意图会定义要执行的操作，例如查看或发送某内容，并且可指定待操作数据的
URI，以及正在启动的组件可能需要了解的信息，例如意图可能会传达对活动的请求，以便显示图像或打开网页。在某些情况下，可以通过启动活动来接收结果，这样活动还会返回意图中的结果，例如可以发出一个意图，让用户选取某位联系人并将其返回给您，返回意图包含指向所选联系人的
URI。对于广播接收器，意图只会定义待广播的通知，例如指示设备电池电量不足的广播只包含指示“电池电量不足”的已知操作字符串。

与活动、服务和广播接收器不同，内容提供程序并非由意图启动，相反其会在成为ContentResolver的请求目标时启动。内容解析程序会通过内容提供程序处理所有直接事务，因此通过提供程序执行事务的组件便无需执行事务，而是改为在
ContentResolver 对象上调用方法。这会在内容提供程序与请求信息的组件之间留出一个抽象层以确保安全。每种组件都有不同的启动方法：

  - 如要启动活动，可以向 startActivity()或startActivityForResult()
    传递意图（当想让活动返回结果时），或者为其安排新任务。

  - 在 Android 5.0（API 级别 21）及更高版本中，可以使用 JobScheduler 类来调度操作。对于早期 Android
    版本，可以通过向 startService()传递Intent来启动服务（或对执行中的服务下达新指令），也可通过向将
    bindService()传递Intent来绑定到该服务。

  - 可以通过向sendBroadcast()、sendOrderedBroadcast() 或 sendStickyBroadcast()
    等方法传递意图来发起广播。

  - 可以通过在ContentResolver上调用query()，对内容提供程序执行查询。

### 清单文件

在安卓系统启动应用组件之前，系统必须通过读取应用的清单文件 (AndroidManifest.xml)
确认组件存在。应用必须在此文件中声明其所有组件，该文件必须位于应用项目目录的根目录中。除了声明应用的组件外，清单文件还有许多其他作用，如：

  - 确定应用需要的任何用户权限，如互联网访问权限或对用户联系人的读取权限。

  - 根据应用使用的 API，声明应用所需的最低 API 级别。

  - 声明应用使用或需要的硬件和软件功能，如相机、蓝牙服务或多点触摸屏幕。

  - 声明应用需要链接的 API 库（安卓框架 API 除外），如地图库。

#### 声明组件

清单文件的主要任务是告知系统应用组件的相关信息，例如清单文件可按如下所示声明活动：

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<manifest ... \>
> 
> \<application android:icon="@drawable/app\_icon.png" ... \>
> 
> \<activity android:name="com.example.project.ExampleActivity"
> 
> android:label="@string/example\_label" ... \>
> 
> \</activity\>
> 
> ...
> 
> \</application\>
> 
> \</manifest\>

码 2‑1

在 \<application\> 元素中，android:icon 属性指向标识应用的图标所对应的资源。在 \<activity\>
元素中，android:name 属性指定活动子类的完全限定类名，android:label
属性指定用作活动的用户可见标签的字符串。必须使用以下元素声明所有应用组件：

  - 活动的 \<activity\> 元素。

  - 服务的 \<service\> 元素。

  - 广播接收器的 \<receiver\> 元素。

  - 内容提供程序的 \<provider\> 元素。

如果未在清单文件中声明源代码中包含的活动、服务和内容提供程序，则这些组件对系统不可见，因此也永远不会运行，不过可以
BroadcastReceiver 对象的形式，在清单中声明或在代码中动态创建广播接收器；以及通过调用
registerReceiver()，在系统中注册广播接收器。

#### 声明功能

如上文启动组件中所述，可以使用意图来启动活动、服务和广播接收器。可以通过在意图中显式命名目标组件（使用组件类名）来使用意图，还可使用隐式意图，通过其来描述要执行的操作类型和待操作数据（可选）。借助隐式意图，系统能够在设备上找到可执行该操作的组件，并启动该组件。如果有多个组件可以执行意图所描述的操作，则由用户选择使用哪一个组件。

注意：如果使用意图来启动服务，请使用显式意图来确保应用的安全性。使用隐式意图启动服务存在安全隐患，因为您无法确定哪些服务将响应意图，且用户无法看到哪些服务已启动。从
Android 5.0（API 级别 21）开始，如果使用隐式意图调用
bindService()，系统会抛出异常。请勿为您的服务声明意图过滤器。

通过将收到的意图与设备上其他应用的清单文件中提供的意图过滤器进行比较，系统便可识别能响应意图的组件。在应用的清单文件中声明活动时，可以选择性地加入声明活动功能的意图过滤器，以便响应来自其他应用的意图。可以将
\<intent-filter\>
元素作为组件声明元素的子项进行添加，从而为的组件声明意图过滤器，例如如果构建的电子邮件应用包含用于撰写新电子邮件的活动，则可通过声明意图过滤器来响应“send”意图（目的是发送新电子邮件），如下方示例所示：

> \<manifest ... \>
> 
> ...
> 
> \<application ... \>
> 
> \<activity android:name="com.example.project.ComposeEmailActivity"\>
> 
> \<intent-filter\>
> 
> \<action android:name="android.intent.action.SEND" /\>
> 
> \<data android:type="\*/\*" /\>
> 
> \<category android:name="android.intent.category.DEFAULT" /\>
> 
> \</intent-filter\>
> 
> \</activity\>
> 
> \</application\>
> 
> \</manifest\>

码 2‑2

如果另一个应用创建包含 ACTION\_SEND 操作的意图并将其传递到
startActivity()，则系统可能会启动活动，以便用户能够草拟并发送电子邮件。

#### 声明要求

安卓设备多种多样，但并非所有设备都提供相同的特性和功能。以防将应用安装在缺少应用所需特性的设备上，必须通过在清单文件中声明设备和软件要求，为该应用支持的设备类型明确定义一个配置文件。其中的大多数声明只是为了提供信息，系统并不会读取其们，但
Google Play 等外部服务会读取其们，以便在用户通过其设备搜索应用时为用户提供过滤功能。例如，如果应用需要相机功能，并使用
Android 2.1（API 级别 7）中引入的 API，必须在清单文件中声明以下要求，如下方示例所示：

> \<manifest ... \>
> 
> \<uses-feature android:name="android.hardware.camera.any"
> 
> android:required="true" /\>
> 
> \<uses-sdk android:minSdkVersion="7" android:targetSdkVersion="19" /\>
> 
> ...
> 
> \</manifest\>

码 2‑3

通过示例中所述的声明，没有相机且 Android 版本低于 2.1 的设备将无法从 Google Play
安装应用。不过可以声明应用使用相机，但并不要求必须使用，在此情况下应用必须将
required 属性设置为 false，并在运行时检查设备是否拥有相机，然后根据需要停用任何相机功能。

### 应用资源

安卓应用并非仅包含代码，其还需要与源代码分离的资源，如图像、音频文件以及任何与应用的视觉呈现有关的内容，例如可以通过 XML
文件定义活动界面的动画、菜单、样式、颜色和布局。借助应用资源，无需修改代码即可轻松更新应用的各种特性，通过提供备用资源集，可以针对各种设备配置（如不同的语言和屏幕尺寸）优化应用。

对于在安卓项目中加入的每一项资源，SDK 构建工具均会定义唯一的整型 ID，可以利用此 ID 来引用资源，这些资源或来自应用代码，或来自 XML
中定义的其他资源。例如，如果应用包含名为 logo.png 的图像文件，保存在 res/drawable/ 目录中，则 SDK 工具会生成名为
R.drawable.logo 的资源 ID，此 ID 映射到应用特定的整型数，可以利用其来引用该图像，并将其插入界面。

如果提供与源代码分离的资源，则其中最重要的一个优点在于，可以提供适用于不同设备配置的备用资源。例如，通过在 XML
中定义界面字符串，可以将字符串翻译为其他语言，并将这些字符串保存在单独的文件中。然后，安卓系统会根据向资源目录名称追加的语言限定符（例如，为法语字符串值追加
res/values-fr/）和用户的语言设置，对界面应用相应的语言字符串。

安卓支持许多不同的备用资源限定符。限定符是资源目录名称中加入的短字符串，用于定义这些资源适用的设备配置，例如应根据设备的屏幕方向和尺寸为活动创建不同的布局。当设备屏幕为纵向（长型）时，可能想要一种垂直排列按钮的布局；但当屏幕为横向（宽型）时，可以按水平方向排列按钮。如要根据方向更改布局，您可以定义两种不同的布局，然后对每个布局的目录名称应用相应的限定符，然后系统会根据当前设备方向自动应用相应的布局。

### 模拟器

安卓模拟器可在计算机上模拟安卓设备，这样就可以在各种设备上以及各个安卓API
级别测试应用，而无需拥有每个实体设备。模拟器几乎可以提供真正的安卓设备所具备的所有功能。可以模拟来电和短信、指定设备的位置、模拟不同的网速、模拟旋转及其他硬件传感器、访问应用商店等等。

从某些方面来看，在模拟器上测试应用比在实体设备上测试要更快、更容易。例如，将数据传输到模拟器的速度比传输到通过 USB
连接的设备更快。模拟器随附了针对各种 安卓手机、平板电脑、Wear OS 和 Android
TV 设备的预定义配置。安卓模拟器除了需要满足 Android Studio 的基本系统要求之外，还需要满足下述其他要求：

  - SDK 工具 26.1.1 或更高版本

  - 64 位处理器

  - Windows：支持 UG（无限制访客）的 CPU

  - HAXM 6.2.1 或更高版本（建议使用 HAXM 7.2.0 或更高版本）

如果要在 Windows 和 Linux 上使用硬件加速，还需要满足以下额外要求：

  - 搭载 Intel 处理器的 Windows 或 Linux 系统：Intel 处理器需要支持 Intel VT-x、Intel
    EM64T (Intel 64) 和 Execute Disable (XD) Bit 功能

  - 搭载 AMD 处理器的 Linux 系统：AMD 处理器需要支持 AMD 虚拟化 (AMD-V) 和 Supplemental
    Streaming SIMD Extensions 3 (SSSE3)

  - 搭载 AMD 处理器的 Windows 系统：需要 Android Studio 3.2 或更高版本以及 2018 年 4 月发布的支持
    Windows Hypervisor Platform (WHPX) 功能的 Windows 10 或更高版本

如需与 Android 8.1（API 级别 27）及更高版本的系统映像配合使用，连接的摄像头必须能够捕捉 720p
的画面。如需在模拟设备上安装 APK 文件，请将 APK
文件拖动到模拟器屏幕上。系统会显示 APK
安装程序对话框。安装完毕后，您可以在应用列表中查看该应用。如需向模拟设备添加文件，请将该文件拖动到模拟器屏幕上。系统会将该文件放在
/sdcard/Download/ 目录下。您可以在 Android Studio 中通过 Device File Explorer
查看该文件，也可以在设备上使用 Downloads 或 Files 应用查找该文件，具体取决于设备的版本。

#### 屏幕导航

可以使用计算机鼠标指针模仿手指在触摸屏上的操作、选择菜单项和输入字段，以及点击按钮和控件，可以使用计算机键盘输入字符以及按下模拟器快捷键。

<table>
<thead>
<tr class="header">
<th>功能</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>滑动屏幕</td>
<td>指向屏幕，按住主鼠标按钮，在屏幕上滑动，然后释放。</td>
</tr>
<tr class="even">
<td>拖动条目</td>
<td>指向屏幕上的某个条目，按住主鼠标按钮，移动对象，然后释放。</td>
</tr>
<tr class="odd">
<td>点按（触摸）</td>
<td>指向屏幕，按主鼠标按钮，然后释放。 例如可以点击文本字段以开始输入内容、选择应用，或者按某个按钮。</td>
</tr>
<tr class="even">
<td>点按两次</td>
<td>指向屏幕，快速按两次主鼠标按钮，然后释放。</td>
</tr>
<tr class="odd">
<td>触摸并按住</td>
<td>指向屏幕上的某个条目，按主鼠标按钮，保持一段时间，然后释放。例如可以打开某个条目的选项。</td>
</tr>
<tr class="even">
<td>输入</td>
<td>您可以使用计算机键盘或者模拟器屏幕上弹出的键盘在模拟器中输入内容。例如，选择某个文本字段后，您可以在其中输入内容。</td>
</tr>
<tr class="odd">
<td>双指张合</td>
<td><p>按 Ctrl可以调出张合手势多点触控界面。鼠标作为第一根手指，鼠标关于锚点对称的点为第二根手指。拖动光标以移动第一个点。</p>
<p>点击鼠标左按钮相当于同时触摸两个点，释放按钮则相当于张开两根手指。</p></td>
</tr>
<tr class="even">
<td>纵向滑动</td>
<td>在屏幕上打开纵向菜单，然后使用滚轮（鼠标滚轮）滚动浏览菜单项，直到看到所需的菜单项。 点击该菜单项即可将其选中。</td>
</tr>
</tbody>
</table>

表 2‑1模拟器屏幕的导航手势

#### 常见操作

如需在模拟器中执行常见操作，请使用右侧的面板（如表 2‑2所述）。可以在模拟器中使用键盘快捷键执行很多常见操作。
如需查看模拟器中快捷键的完整列表，请按 F1 键，在 “Extended
controls”窗口中打开“Help”窗格。

<table>
<thead>
<tr class="header">
<th>功能</th>
<th>图标</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Close</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image1.png" style="width:0.18013in;height:0.14179in" alt="“Close”图标" /></td>
<td>关闭模拟器。</td>
</tr>
<tr class="even">
<td>Minimiz</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image2.png" style="width:0.21935in;height:0.11194in" alt="“Minimize”图标" /></td>
<td>最小化模拟器窗口。</td>
</tr>
<tr class="odd">
<td>Resize</td>
<td></td>
<td>像调整任何其他操作系统窗口一样调整模拟器的大小。模拟器将保持适合您设备的宽高比。</td>
</tr>
<tr class="even">
<td>Power</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image3.png" style="width:0.19403in;height:0.16483in" alt="“Power”图标" /></td>
<td>点击即可开启或关闭屏幕。<br />
点击并按住即可开启或关闭设备。</td>
</tr>
<tr class="odd">
<td>Volume up</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image4.png" style="width:0.18648in;height:0.17164in" alt="“Volume Up”图标" /></td>
<td>点击即可显示一个滑块控件并将音量调高。再次点击即可进一步调高音量，也可以使用滑块控件调整音量。</td>
</tr>
<tr class="even">
<td>Volume down</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image5.png" style="width:0.20149in;height:0.16903in" alt="“Volume Down”图标" /></td>
<td>点击即可显示一个滑块控件并将音量调低。再次点击即可进一步调低音量，也可以使用滑块控件调整音量。</td>
</tr>
<tr class="odd">
<td>Rotate left</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image6.png" style="width:0.20851in;height:0.21642in" alt="“Rotate Left”图标" /></td>
<td>将设备逆时针旋转 90 度。</td>
</tr>
<tr class="even">
<td>Rotate right</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image7.png" style="width:0.21642in;height:0.21642in" alt="“Rotate Right”图标" /></td>
<td>将设备顺时针旋转 90 度。</td>
</tr>
<tr class="odd">
<td>Take screenshot</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image8.png" style="width:0.17164in;height:0.15703in" alt="“Take Screenshot”图标" /></td>
<td>点击即可对设备进行屏幕截图。</td>
</tr>
<tr class="even">
<td>Enter zoom mode</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image9.png" style="width:0.20815in;height:0.20149in" alt="“Enter Zoom Mode”图标" /></td>
<td><p>点击即可使光标变为缩放图标。如需退出缩放模式，再次点击该按钮即可。</p>
<p>在缩放模式下放大和缩小：</p>
<ul>
<li><p>左键点击屏幕即可放大 25%，最多可放大至虚拟设备屏幕分辨率的两倍左右。</p></li>
<li><p>右键点击即可缩小。</p></li>
<li><p>左键点击并拖动即可选择要放大的方形区域。</p></li>
<li><p>右键点击并拖动选择框即可重置为默认缩放级别。</p></li>
</ul>
<p>如需在缩放模式下平移，请在按住 Ctrl 键（在 Mac 上，按住 Command 键）的同时按键盘上的箭头键。</p>
<p>如需在缩放模式下点按设备屏幕，请在按住 Ctrl 键的同时点击鼠标（在 Mac 上，按住 Command 键的同时点击鼠标）。</p></td>
</tr>
<tr class="odd">
<td>Back</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image10.png" style="width:0.17577in;height:0.18657in" alt="“Back”图标" /></td>
<td>返回上一个屏幕，或者关闭对话框、选项菜单、“Notifications”面板或屏幕上的键盘。</td>
</tr>
<tr class="even">
<td>Home</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image11.png" style="width:0.17569in;height:0.18649in" alt="“Home”图标" /></td>
<td>返回主屏幕。</td>
</tr>
<tr class="odd">
<td><p>Overview</p>
<p>(Recent Apps)</p></td>
<td><img src="md/media/chapter02_ui_design_basics/media/image12.png" style="width:0.17911in;height:0.17911in" alt="“Overview”图标" /></td>
<td>点按即可打开最近用过的应用的缩略图列表。如需打开某个应用，点按相应缩略图即可。如需从列表中移除某个缩略图，请向左或向右滑动该缩略图。Wear OS 不支持此按钮。</td>
</tr>
<tr class="even">
<td>Fold</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image13.png" style="width:0.1908in;height:0.16418in" alt="“Fold”图标" /></td>
<td>对于可折叠设备，折叠设备以显示其较小的屏幕配置。</td>
</tr>
<tr class="odd">
<td>Unfold</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image14.png" style="width:0.18213in;height:0.15672in" alt="“Unfold”图标" /></td>
<td>对于可折叠设备，展开设备以显示其较大的屏幕配置。</td>
</tr>
<tr class="even">
<td>菜单</td>
<td></td>
<td>按 Ctrl+M即可模拟“Menu”按钮。</td>
</tr>
<tr class="odd">
<td>More</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image15.png" style="width:0.23117in;height:0.10448in" alt="“More”图标" /></td>
<td>点击即可访问下一个表中介绍的其他功能和设置。</td>
</tr>
</tbody>
</table>

表 2‑2 模拟器中的常见操作

## 理解活动

活动是安卓的四大基本组件之一。通过活动，用户可以与移动终端进行交互，使用安卓应用程序做一些事情，如拨打电话、拍照、发送电子邮件或查看地图等等。活动也可以看作是一个特定的窗口，输入框、按钮等各种视图控件能够按需求进行不同的排列。这种窗口通常填满整个屏幕，但可能会小于屏幕或者浮在其他窗口之上，例如对话框。

所有的活动都是从安卓提供的Activity类继承而来。一个应用程序通常由一个或多个活动组成。在应用程序中，通常指定一个主活动，其是应用程序启动时，首先呈现给用户的界面。应用通过主活动，根据不同功能启动其他的活动。这些活动之间根据应用程序的逻辑功能可以实现相互调用启动。如果一个活动启动了另一个活动，则自身的状态发生改变，处于停止stopped状态，新的活动接替其成为用户可操作的界面。通常情况下，一个应用不会将所有的功能在一个活动中实现。

在第一章编写安卓应用程序显示“你好移动世界！”时，用到了活动。下面来讨论一下创建和使用活动的基本过程，了解活动在用户操作过程中呈现的状态和各种状态的转换过程，以及对应的系统回调方法。要创建在安卓应用程序中可运行的活动，必须要实现四个任务：

  - 命名并定义活动的子类

  - 实现用户界面

  - 在Manifest文件中声明这个活动

  - 测试运行

### 定义活动

要创建一个用户界面活动，必须定义一个子类来继承Activity类，并根据界面的功能和状态变化，在其回调方法中编写相应的代码。在安卓定义活动组件时，针对用户界面的不同状态变化，为其定义了一系列不同的回调方法。例如用户界面创建、改变、恢复或销毁的动作，都对应活动中不同的回调方法。所谓回调方法，就是在活动状态变化时，安卓系统会自动调用在活动中预先定义的对应方法，执行其中的代码，因为是系统反向调用子类中定义方法，实现其功能，所以称为回调。因此定义子类时，可以在回调方法中定义需要的功能，系统就可以在用户界面状态变化时，调用应用程序中活动的对应回调方法，来实现其功能。例如，某个活动需要在界面恢复时，重新读取数据库的信息，就可以在此活动的onResume()回调方法中定义重新读取数据库的操作。这样当这个活动界面恢复时，系统就会自动调用其的onResume()，运行里面定义的代码，实现其功能。在活动的回调方法中，有两个最重要的回调方法onCreate()和onPause()。onCreate()是在系统创建活动时，第一个调用这个方法，并且执行其中的代码，因此必须实现这个方法。这部分代码主要是进行变量的初始化，完成活动的初始化，其中最主要的是必须调用setContentView()方法，为活动的用户界面定义布局，就是初始化显示界面。一旦onCreate()方法调用完成后，活动的状态不会停留在Created，系统会立刻接着调用活动的另两个回调方法onStart()方法和onResume()
方法，活动的状态很快会进入到Resumed状态，也就是运行状态。onPause()是当用户离开当前的活动时，系统调用此方法。

下面，为了讨论活动的创建、使用和运行状态，在Android Studio建立一个新的应用项目，命名为“My First
App”，主活动命名为MainActivity。在完成活动初步创建之后，就进行下一步，根据应用的功能设计用户图形界面，步骤如下：

(1)在“Welcome to Android Studio”窗口中，点击“Create New
Project”，如果已打开一个项目，依次选择“File \> New \>
New Project”；

(2)在“Select a Project Template”窗口中，选择“Empty活动”，然后点击 “Next”；

(3)在“Configure your project”窗口中，完成以下操作：

  - 在 Name 字段中输入“My First App”。

  - 在 Package name 字段中输入“com.example.myfirstapp”。

  - 如果您想将项目放在其他文件夹中，请更改其 Save 位置。

  - 从 Language 下拉菜单中选择 Java。

  - 在 Minimum SDK 字段中选择您希望应用支持的最低 Android 版本。

  - 如果应用需要旧版库支持，请选中 Use legacy android.support libraries 复选框。

  - 其他选项保持原样。

(4)点击“Finish”。

首先，确保已打开“Project”窗口（依次选择“View \> Tool Windows \>
Project”），并从该窗口顶部的下拉列表中选择“Android”视图，随后可以看到以下文件：

  - app \> java \> com.example.myfirstapp \> MainActivity

这是主活动，其是应用的入口点，当构建和运行应用时，系统会启动此活动的实例并加载其布局。

  - app \> res \> layout \> activity\_main.xml

此 XML 文件定义了活动界面的布局，其包含一个 TextView 元素，其中具有“Hello, World\!”文本。

  - app \> manifests \> AndroidManifest.xml

清单文件描述了应用的基本特性并定义了每个应用组件。

  - Gradle Scripts \> build.gradle

有两个使用此名称的文件：一个针对项目“Project: My First App”，另一个针对应用模块“Module:
My\_First\_App.app”。每个模块均有自己的 build.gradle 文件，但此项目当前仅有一个模块。使用每个模块的
build.gradle 文件控制 Gradle 插件构建应用的方式。

### 构建简单界面

在本节课中将学习如何使用 Android Studio 布局编辑器创建包含一个文本框和一个按钮的布局（图
2‑1），如何在点按该按钮时让应用将文本框的内容发送到其他活动。

![](md/media/chapter02_ui_design_basics/media/image16.png)

图 2‑1 最终布局的屏幕截图

安卓为程序员提供了三种用户界面设计的方法：Java代码“编程式”设计实现、XML文件编写设计和图形化界面设计。如果使用Java代码直接在源代码中构建应用程序的用户界面，在编写代码过程中必须对界面的层次关系十分清晰，并逐个定义这些用户图形界面组件的属性和关系，这在界面比较复杂、组件比较多时，对程序员是一个考验。在界面美观、组件排列合理、组件位置微调、风格统一等细节方面可能会耗费程序员较多的时间，可能比预计的时间多得多。这种方式对于界面的开发是很不方便的，因为一些小的布局变化都有可能导致源码的修改，并且需要重新编译，因此这种方法在安卓的用户图形界面设计并不推荐，

设计实现用户界面的第二种方式，XML文件编写设计，是使用Android
Studio的XML文件编辑器打开活动对应的XML布局文件，在文件中编写代码来描述界面的布局、控件和相关的属性。我们在第一章中使用的就是这种方法。由于XML文件是一个层次化结构的文件，因此布局和控件的关系十分清楚，语法比较简单。而且组件的标识、属性和位置的设置集中在一起，布局和控件不涉及应用程序的逻辑，只是单纯的排列和设计，也就能很方便、快速地完成用户界面的布局了，修改起来也很容易定位。缺点是在界面比较复杂、组件比较多时也很难兼顾美观，如果只能在活动运行时才能看到效果，调整起来同样也很耗费时间。

图形化界面设计是使用Android
Studio提供的图形化界面设计工具，能够把工具提供的布局和控件直接拖拽到手机界面上合适的位置，直观显示出来，并且通过属性设置界面，直接设置组件的属性。在设计过程中，随着布局和控件的添加，属性的修改，XML布局文件的代码也自动生成。使用第二种方式直接编写代码的XML布局文件，也可以通过手机的模拟界面，直观看到实际显示效果。

如果要使用Android Studio的图形化界面设计工具，首先打开需要设计的XML布局文件，然后点击右上角标注的Design标签（图
2‑2），就可以看到手机模拟界面的窗口了。从Palette拖拽组件到手机屏幕，在Palette窗口中（图
2‑3），列出了Layouts、Widgets、Text、Buttons和Containers等布局和控件。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image17.png" style="width:1.82292in;height:0.36458in" /></p>
<p>图 2‑2 Design标签</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image18.png" style="width:2.43235in;height:2.73006in" /></p>
<p>图 2‑3 Palette窗口</p></td>
</tr>
</tbody>
</table>

安卓应用的界面以布局和微件的层次结构形式构建而成。布局是 ViewGroup 对象，即控制其子视图在屏幕上的放置方式的容器；微件是 View 对象，即按钮和文本框等界面组件（图
2‑4）。

![](md/media/chapter02_ui_design_basics/media/image19.png)

 图 2‑4 ViewGroup 对象如何在布局中形成分支并包含 View 对象的图示

安卓提供了 ViewGroup 和 View 类的 XML 词汇表，因此界面的大部分内容都在 XML 文件中定义。本节课将介绍如何使用
Android Studio 的布局编辑器创建布局，而不是编写 XML 代码，布局编辑器会在拖放视图构建布局时为编写XML代码。

#### 布局编辑器

首先，请按照以下步骤设置工作区：

(1)在Project窗口中，依次打开 “app \> res \> layout \> activity\_main.xml”；

(2)若要给布局编辑器留出空间，请隐藏 Project窗口。为此，请依次选择 “View \> Tool Windows \>
Project”，或直接点击 Android Studio 屏幕左侧的 Project；

(3)如果编辑器显示 XML 源代码，请点击窗口右上角的 Design 标签页；

(4)点击 ![](md/media/chapter02_ui_design_basics/media/image20.png) （“Select
Design Surface”），然后选择 Blueprint；

(5)点击布局编辑器工具栏中的 ![](md/media/chapter02_ui_design_basics/media/image21.png) （“View
Options”），并确保选中 “Show All Constraints”；

(6)确保 Autoconnect 处于关闭状态，当 Autoconnect
处于关闭状态时，工具栏中的提示会显示 ![](md/media/chapter02_ui_design_basics/media/image22.png) （“Enable
Autoconnection to Parent”）；

(7)点击工具栏中的 ![](md/media/chapter02_ui_design_basics/media/image23.png) （“Default
Margins”），然后选择 16，如果需要可以稍后调整每个视图的外边距。

(8)点击工具栏中的 ![](md/media/chapter02_ui_design_basics/media/image24.png) （“Device
for Preview”），然后选择 “5.5, 1440 × 2560, 560 dpi (Pixel XL)”。

布局编辑器现在如图 2‑5所示。

![](md/media/chapter02_ui_design_basics/media/image25.png)

图 2‑5显示 activity\_main.xml 的布局编辑器

左下方的 Component
Tree 面板显示布局的视图层次结构。在本例中根视图是 ConstraintLayout，其仅包含一个 TextView 对象。ConstraintLayout 是一种布局，其根据同级视图和父布局的约束条件定义每个视图的位置。这样一来，使用扁平视图层次结构既可以创建简单布局，又可以创建复杂布局。这种布局无需嵌套布局。嵌套布局是布局内的布局（图
2‑4），会增加绘制界面所需的时间。

![](md/media/chapter02_ui_design_basics/media/image26.png)

图 2‑6 ConstraintLayout 内放有两个视图的图示

例如可以声明以下布局，如图 2‑6所示：

  - 视图A距离父布局顶部 16 dp

  - 视图A距离父布局左侧 16 dp

  - 视图B距离视图 A 右侧 16 dp

  - 视图B与视图A顶部对齐

在后面几部分中，将构建一个与图 2‑6中的布局类似的布局。

#### 添加文本框

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image27.png" style="width:0.28566in;height:0.91411in" /></p>
<p>图 2‑7 Zoom 按钮</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image28.png" style="width:2.49693in;height:0.83524in" /></p>
<p>图 2‑8按照到父布局顶部和左侧的距离约束文本框</p></td>
</tr>
</tbody>
</table>

按照下面的步骤添加文本框：

(1)首先需要移除布局中已有的内容，在 Component Tree 面板中点击 TextView，然后按 Delete 键；

(2)在Palette面板中，点击 Text 以显示可用的文本控件；

(3)将“Plain Text”拖动到设计编辑器中，并将其放在靠近布局顶部的位置，这是一个接受纯文本输入的 EditText 微件；

(4)点击设计编辑器中的视图，现在可以在每个角上看到调整视图大小的正方形手柄，并在每个边上看到圆形约束锚点。为了更好地控制，可能需要放大编辑器，为此使用布局编辑器工具栏中的 Zoom 按钮（图
2‑7）；

(5)点击并按住顶边上的锚点，将其向上拖动直至其贴靠到布局顶部，然后将其释放。这是一个约束条件，其会将视图约束在已设置的默认外边距内。在本例中将其设置为距离布局顶部
16 dp；

(6)使用相同的过程创建一个从视图左侧到布局左侧的约束条件，结果应如图 2‑8所示。

#### 添加按钮

![](md/media/chapter02_ui_design_basics/media/image29.png)

 图 2‑9 按照到文本框右侧的距离以及文本框基线来约束按钮

(1)在 Palette 面板中，点击 Buttons；

(2)将 Button 微件拖到设计编辑器中，并将其放在靠近右侧的位置；

(3)创建一个从按钮左侧到文本框右侧的约束条件；

(4)如需按水平对齐约束视图，创建一个文本基线之间的约束条件，为此右键点击按钮，然后选择 “Show
Baseline” ![在布局编辑器中显示基准操作](md/media/chapter02_ui_design_basics/media/image30.png)。基线锚点显示在按钮内部，点击并按住此锚点，然后将其拖动到相邻文本框中显示的基线锚点上，结果应如图
2‑9所示。

注意：您还可以根据顶边或底边实现水平对齐。但按钮的图片周围有内边距，因此如果以这种方式对齐，那么其们看上去是没有对齐的。

#### 更改界面字符串

若要预览界面，请点击工具栏中的 ![](md/media/chapter02_ui_design_basics/media/image20.png) (“Select
Design Surface”)，然后选择 Design。请注意，文本输入和按钮标签应设置为默认值。若要更改界面字符串，请按以下步骤操作：

(1)打开 Project窗口，然后打开“app \> res \> values \>
strings.xml”。这是一个字符串资源文件，可在此文件中指定所有界面字符串，可以利用该文件在一个位置管理所有界面字符串，使字符串的查找、更新和本地化变得更加容易；

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image31.png" style="width:2.67894in;height:0.61194in" /></p>
<p>图 2‑10 字符串管理界面</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image32.png" style="width:2.69419in;height:1.23691in" /></p>
<p>图 2‑11 用于添加新字符串的对话框</p></td>
</tr>
</tbody>
</table>

(2)点击窗口顶部的“Open editor”，此时将打开 “Translations
Editor”，其提供了一个可以添加和修改默认字符串的简单界面，其还有助于让所有已翻译的字符串井然有序（图
2‑10）；

(3)点击 ![](md/media/chapter02_ui_design_basics/media/image33.png) (“Add
Key”) 可以创建一个新字符串作为文本框的提示文本，此时会打开如图 2‑11所示的窗口，在 Add Key 对话框中，完成以下步骤：

  - 在 Key 字段中输入“edit\_message”。

  - 在 Default Value 字段中输入“Enter a message”。

  - 点击 OK。

(4)再添加一个名为“button\_send”且值为“Send”的键。

现在，可以为每个视图设置这些字符串。若要返回布局文件，请点击标签页栏中的 activity\_main.xml，然后添加字符串，如下所示：

(1)点击布局中的文本框，如果右侧还未显示 Attributes 窗口，请点击右侧边栏上的 Attributes；

(2)找到 text 属性（当前设为“Name”）并删除相应的值；

(3)找到 hint 属性，然后点击文本框右侧的![](md/media/chapter02_ui_design_basics/media/image34.png)(“Pick
a Resource”)，在显示的对话框中，双击列表中的 edit\_message；

(4)点击布局中的按钮，找到其 text 属性（当前设为“Button”），然后点击![](md/media/chapter02_ui_design_basics/media/image34.png)(“Pick
a Resource”)，并选择 button\_send。

#### 大小可灵活调整

若要创建一个适应不同屏幕尺寸的布局，需要让文本框拉伸以填充去除按钮和外边距后剩余的所有水平空间。继续操作之前，点击工具栏中的 ![](md/media/chapter02_ui_design_basics/media/image20.png) (Select
Design Surface)，然后选择 Blueprint，若要让文本框大小可灵活调整按以下步骤操作：

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image35.png" style="width:3.52505in;height:0.85821in" /></p>
<p>图 2‑12选择 Create Horizontal Chain 后所得到的结果</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image36.png" style="width:2.12484in;height:1.48368in" /></p>
<p>图 2‑13点击以将宽度更改为 Match Constraints</p></td>
</tr>
</tbody>
</table>

![](md/media/chapter02_ui_design_basics/media/image37.png)

图 2‑14 文本框现在拉伸以填充剩余空间

(1)选择两个视图。若要执行此操作，请点击一个视图，在按住 Shift 键的同时点击另一个视图，然后右键点击任一视图并依次选择 “Chains
\> Create Horizontal Chain”。布局随即显示出来，如图
2‑12所示。链是两个或多个视图之间的双向约束条件，可采用一致的方式安排链接的视图。

(2)选择按钮并打开 Attributes 窗口，然后使用 Constraint Widget 将右外边距设为 16 dp；

(3)点击文本框以查看其属性，然后点击宽度指示器两次，确保将其设置为锯齿状线 (Match Constraints)，如图
2‑13中的标注 1 所示。“Match
constraints”表示宽度将延长以符合水平约束条件和外边距的定义，因此文本框将拉伸以填充去除按钮和所有外边距后剩余的水平空间。

现在布局已经完成，如图 2‑14所示。如果布局看起来不像预期的那样，点击下方的查看最终布局 XML，查看 XML
应该是什么样子，将其与在 Code 标签页中看到的内容进行比较，如果属性以不同的顺序显示也没关系，查看最终布局
XML文件：

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<androidx.constraintlayout.widget.ConstraintLayout
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> xmlns:app="http://schemas.android.com/apk/res-auto"
> 
> xmlns:tools="http://schemas.android.com/tools"
> 
> android:layout\_width="match\_parent"
> 
> android:layout\_height="match\_parent"
> 
> tools:context=".MainActivity"\>
> 
> \<EditText
> 
> android:id="@+id/editTextTextPersonName"
> 
> android:layout\_width="0dp"
> 
> android:layout\_height="wrap\_content"
> 
> android:layout\_marginStart="16dp"
> 
> android:layout\_marginLeft="16dp"
> 
> android:layout\_marginTop="16dp"
> 
> android:ems="10"
> 
> android:hint="@string/edit\_message"
> 
> android:inputType="textPersonName"
> 
> app:layout\_constraintEnd\_toStartOf="@+id/button"
> 
> app:layout\_constraintHorizontal\_bias="0.5"
> 
> app:layout\_constraintStart\_toStartOf="parent"
> 
> app:layout\_constraintTop\_toTopOf="parent" /\>
> 
> \<Button
> 
> android:id="@+id/button"
> 
> android:layout\_width="wrap\_content"
> 
> android:layout\_height="wrap\_content"
> 
> android:layout\_marginEnd="16dp"
> 
> android:layout\_marginStart="16dp"
> 
> android:text="@string/button\_send"
> 
> app:layout\_constraintBaseline\_toBaselineOf="@+id/editTextTextPersonName"
> 
> app:layout\_constraintEnd\_toEndOf="parent"
> 
> app:layout\_constraintHorizontal\_bias="0.5"
> 
> app:layout\_constraintStart\_toEndOf="@+id/editTextTextPersonName" /\>
> 
> \</androidx.constraintlayout.widget.ConstraintLayout\>

码 2‑4

点击 Run
'app'![](md/media/chapter02_ui_design_basics/media/image38.png) 以安装并运行应用，该按钮仍然没有任何作用。如需构建在点按该按钮后会启动的另一个活动，继续下一节。

### 调用活动

目前已经构建了一个应用，该应用将显示一个活动（单个屏幕），其中包含一个文本字段和一个发送按钮，下面将向 MainActivity 添加一些代码，以便在用户点发送按钮时启动一个显示消息的新活动。

#### 响应按钮

可按照以下步骤，向 MainActivity 类添加一个在用户点发送按钮时调用的方法：

(1)在“app \> java \> com.example.myfirstapp \>
MainActivity”文件中，添加以下 sendMessage() 方法桩：

> public class MainActivity extends AppCompatActivity {
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.activity\_main);
> 
> }
> 
> /\*\* Called when the user taps the Send button \*/
> 
> public void sendMessage(View view) {
> 
> // Do something in response to button
> 
> }
> 
> }

码 2‑5

可能会看到一条错误，因为 Android Studio
无法解析用作方法参数的 View 类。若要清除错误，请点击 View 声明，将光标置于其上，然后按 Alt+Enter进行快速修复。如果出现一个菜单，请选择“Import
class”；

(2)返回到 activity\_main.xml 文件，并从该按钮调用此方法：

  - 选择布局编辑器中的相应按钮。

  - 在 Attributes 窗口中，找到 onClick 属性，并从其下拉列表中选择 sendMessage
    \[MainActivity\]。

现在，当用户点按该按钮时，系统将调用 sendMessage() 方法。请注意此方法中提供的详细信息。系统需要这些信息来识别此方法是否与 android:onClick 属性兼容。具体来说，此方法具有以下特性：

  - 公开

  - 返回值为空

  - View 是唯一的参数。这是在第 1 步结束时点击的 View 对象。

(3)接下来，填写此方法，以读取文本字段的内容，并将该文本传递给另一个活动。

#### 构建intent

Intent 是在相互独立的组件（如两个活动）之间提供运行时绑定功能的对象。Intent 表示应用执行某项操作的意图。可以使用意图执行多种任务，但在本节中，Intent
将用于启动另一个活动。在 MainActivity 中，添加 EXTRA\_MESSAGE 常量和 sendMessage() 代码，如下所示：

> public class MainActivity extends AppCompatActivity {
> 
> public static final String EXTRA\_MESSAGE =
> "com.example.ch02.myapplication.MESSAGE";
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.activity\_main);
> 
> }
> 
> public void sendMessage(View view) {
> 
> Intent intent = new Intent(this, DisplayMessageActivity.class);
> 
> EditText editText = (EditText)
> findViewById(R.id.editTextTextPersonName);
> 
> String message = editText.getText().toString();
> 
> intent.putExtra(EXTRA\_MESSAGE, message);
> 
> startActivity(intent);
> 
> }
> 
> }

码 2‑6

预计 Android Studio 会再次遇到 “Cannot resolve
symbol”错误，如需清除这些错误，请按 Alt+Enter，最后应导入以下内容：

> import androidx.appcompat.app.AppCompatActivity;
> 
> import android.content.Intent;
> 
> import android.os.Bundle;
> 
> import android.view.View;
> 
> import android.widget.EditText;

码 2‑7

DisplayMessageActivity 仍有错误，但没有关系将在下一部分中修复该错误。sendMessage() 将发生以下情况：

  - Intent 构造函数会获取两个参数：Context 和 Class。

首先使用 Context 参数，因为活动类是 Context 的子类。在本例中系统将意图传递到另一个应用组件，DisplayMessageActivity.class参数是要启动的活动。

  - putExtra() 方法将 EditText 的值添加到意图。Intent 能够以称为“extra”的键值对形式携带数据类型。

键是一个公共常量 EXTRA\_MESSAGE，因为下一个活动将使用该键检索文本值。为“Intent
Extra”定义键时，最好使用应用的软件包名称作为前缀，这样可以确保这些键是独一无二的，这在应用需要与其他应用进行交互时会很重要。

  - startActivity() 方法将启动一个由意图指定的 DisplayMessageActivity 实例，接下来，需要创建该类。

#### 第二个活动

若要创建第二个活动，请按以下步骤操作：

(1)在Project窗口中，右键点击 app 文件夹，然后依次选择“New \>活动\> Empty活动”；

(2)在“Configure活动”窗口中，输入“DisplayMessageActivity”作为 “活动Name”，将所有其他属性保留为默认设置，然后点击 Finish。

Android Studio 会自动执行下列三项操作：

  - 创建 DisplayMessageActivity 文件。

  - 创建 DisplayMessageActivity文件对应的布局文件 activity\_display\_message.xml。

  - 在 AndroidManifest.xml 中添加所需的 \<activity\> 元素。

如果运行应用并点按第一个活动上的按钮，将启动第二个活动，但其为空。这是因为第二个活动使用模板提供的空布局。

#### 添加文本视图

![](md/media/chapter02_ui_design_basics/media/image39.png)

图 2‑15 位于布局顶部中心的文本视图。

在新Activity中包含一个空白布局文件，按以下步骤操作，在显示消息的位置添加一个文本视图：

(1)打开“ app \> res \> layout \> activity\_display\_message.xml ”文件。

(2)点击工具栏中的“Enable Autoconnection to
Parent” ![](md/media/chapter02_ui_design_basics/media/image22.png)。系统将启用
Autoconnect，参见图 2‑15。

(3)在 Palette 面板中，点击 Text，将 TextView 拖动到布局中，然后将其放置在靠近布局顶部中心的位置，使其贴靠到出现的垂直线上。Autoconnect
将添加左侧和右侧约束条件，以便将该视图放置在水平中心位置。

(4)再创建一个从文本视图顶部到布局顶部的约束条件，使该视图如图 2‑15中所示。

或者可以对文本样式进行一些调整，方法是在 Attributes 窗口的 “Common
Attributes”面板中展开 textAppearance，然后更改 textSize 和 textColor 等属性。

#### 显示消息

在此步骤中将修改第二个活动以显示第一个活动传递的消息。

(1)在 DisplayMessageActivity 中，将以下代码添加到 onCreate() 方法中：

> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.activity\_display\_message);
> 
> // Get the Intent that started this activity and extract the string
> 
> Intent intent = getIntent();
> 
> String message = intent.getStringExtra(MainActivity.EXTRA\_MESSAGE);
> 
> // Capture the layout's TextView and set the string as its text
> 
> TextView textView = findViewById(R.id.textView);
> 
> textView.setText(message);
> 
> }

码 2‑8

(2)按 Alt+Enter导入其他所需的类：

> import androidx.appcompat.app.AppCompatActivity;
> 
> import android.content.Intent;
> 
> import android.os.Bundle;
> 
> import android.widget.TextView;

码 2‑9

#### 添加向上导航

在应用中，不是主入口点的每个屏幕（是指所有不是主屏幕的屏幕）都必须提供导航功能，以便将用户引导至应用层次结构中的逻辑父级屏幕，为此在应用栏中添加向上按钮。若要添加向上按钮，需要在 AndroidManifest.xml 文件中声明哪个活动是逻辑父级。打开“ app
\> manifests \>
AndroidManifest.xml” 文件，找到 DisplayMessageActivity 的 \<activity\> 标记，然后将其替换为以下代码：

> \<activity android:name=".DisplayMessageActivity"
> 
> android:parentActivityName=".MainActivity"\>
> 
> \<\!-- The meta-data tag is required if you support API level 15 and
> lower --\>
> 
> \<meta-data
> 
> android:name="android.support.PARENT\_ACTIVITY"
> 
> android:value=".MainActivity" /\>
> 
> \</activity\>

码 2‑10

安卓系统现在会自动向应用栏添加向上按钮。

#### 运行应用

点击工具栏中的“Apply Changes”
![](md/media/chapter02_ui_design_basics/media/image40.png)以运行应用，当应用打开后在文本字段中输入一条消息，点按发送即会看到该消息显示在第二个活动中。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image41.png" style="width:2.56716in;height:1.56989in" /></p>
<p>图 2‑16 输入屏幕</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image42.png" style="width:2.58535in;height:1.56944in" /></p>
<p>图 2‑17 输出结果</p></td>
</tr>
</tbody>
</table>

到此就结束了，已经构建了简单的界面，并且可以调用另一个界面的安卓应用。

### 生命周期

安卓平台主要是为移动终端开发的操作系统，移动终端的特性就是应该能随时在未完成当前任务的时候，切换到其他任务中，再次回来以后还可以继续完成刚才没有完成的任务，为什么设置活动的生命周期呢？

先看一个典型的例子：在编写短信时，有一个紧急电话打过来，用户必须要接这个电话；如果接完电话后，肯定希望继续编辑刚才的短信完成这个任务。为了完成类似的任务，安卓系统需要同时执行多个程序。但对于移动终端这种有限资源的平台来说，同时执行多个程序有可以提高用户友好性，但是也有其严重的缺点，即每多执行一个应用程序，就会多耗费一些系统内存。手机里的内存是相当有限的，当同时执行的程序过多，或是关闭的程序没有正确释放掉内存，执行系统时就会觉得越来越慢甚至不稳定。为了解决个问题，安卓引入了生命周期的机制来管理应用程序，见**图
2‑18**。

![](md/media/chapter02_ui_design_basics/media/image43.png)

**图** **2‑18 Activity的生命周期**

安卓应用程序的生命周期是由系统框架进行管理，不是由应用程序直接控制，安卓系统的Dalvik虚拟机会依照内存状况和Activity的使用状态，来自动管理内存的使用。**图
2‑18**说明了活动生命周期中各个状态，以及在状态转换过程中系统会调用的方法。为了在活动生命周期的各个阶段之间导航转换，活动类提供六个核心回调方法：onCreate()、onStart()、onResume()、onPause()、onStop()和onDestroy()。当活动进入新状态时，系统会调用其中的回调方法。活动状态的转换是由于用户的操作或其他原因引起的，当Activity状态发生转换时，相关方法中的代码就会执行，例如在Activity创建启动时，会依次调用其onCreate()、onStart()、onResume方法中的代码；当Activity从运行状态转换成暂停状态时，会调用其onPause()方法中的代码。

当用户开始离开活动时，系统会调用方法来销毁该活动。在某些情况下，此销毁只是部分销毁；活动仍然驻留在内存中（例如当用户切换至另一应用时），并且仍然可以返回到前台。如果用户返回到该活动，活动会从用户离开时的位置继续运行。除了少数例外，应用在后台运行时会受到限制，无法启动活动。系统终止给定进程及其中活动的可能性取决于当时活动的状态。活动状态和从内存中弹出
会更详细地介绍状态与弹出漏洞之间的关系。根据活动的复杂程度，可能不需要实现所有生命周期方法。但是，请务必了解每个方法，并实现能够确保应用按预期方式运行的方法，这非常重要。本书将活动的生命周期定义为四种状态：

#### 激活状态

活动在屏幕的前端状态称为激活或者运行状态，这个过程可以包括三个回调方法。

  - onCreate()

必须实现此回调，其会在系统首次创建活动时触发。活动会在创建后进入已创建状态。在 onCreate() 方法中，您需执行基本应用启动逻辑，该逻辑在活动的整个生命周期中只应发生一次。例如，onCreate() 的实现可能会将数据绑定到列表，将活动与 ViewModel 相关联，并实例化某些类作用域变量。此方法会接收 savedInstanceState 参数，后者是包含活动先前保存状态的 Bundle 对象。如果活动此前未曾存在，Bundle 对象的值为
null。如果有一个生命周期感知型组件与Activity 生命周期相关联，该组件将收到 ON\_CREATE 事件。系统将调用带有
@OnLifecycleEvent
注释的方法，以使生命周期感知型组件可以执行已创建状态所需的任何设置代码。onCreate() 方法的以下示例显示执行活动某些基本设置的一些代码，例如声明界面（在
XML 布局文件中定义）、定义成员变量，以及配置某些界面。在本示例中，系统通过将文件的资源
ID（R.layout.main\_activity）传递给 setContentView() 来指定 XML 布局文件。

> TextView textView;
> 
> // some transient state for the activity instance
> 
> String gameState;
> 
> @Override
> 
> public void onCreate(Bundle savedInstanceState) {
> 
> // call the super class onCreate to complete the creation of activity
> like
> 
> // the view hierarchy
> 
> super.onCreate(savedInstanceState);
> 
> // recovering the instance state
> 
> if (savedInstanceState \!= null) {
> 
> gameState = savedInstanceState.getString(GAME\_STATE\_KEY);
> 
> }
> 
> // set the user interface layout for this activity
> 
> // the layout file is defined in the project
> res/layout/main\_activity.xml file
> 
> setContentView(R.layout.main\_activity);
> 
> // initialize member TextView so we can manipulate it later
> 
> textView = (TextView) findViewById(R.id.text\_view);
> 
> }
> 
> // This callback is called only when there is a saved instance that is
> previously saved by using
> 
> // onSaveInstanceState(). We restore some state in onCreate(), while
> we can optionally restore
> 
> // other state here, possibly usable after onStart() has completed.
> 
> // The savedInstanceState Bundle is same as the one used in
> onCreate().
> 
> @Override
> 
> public void onRestoreInstanceState(Bundle savedInstanceState) {
> 
> textView.setText(savedInstanceState.getString(TEXT\_VIEW\_KEY));
> 
> }
> 
> // invoked when the activity may be temporarily destroyed, save the
> instance state here
> 
> @Override
> 
> public void onSaveInstanceState(Bundle outState) {
> 
> outState.putString(GAME\_STATE\_KEY, gameState);
> 
> outState.putString(TEXT\_VIEW\_KEY, textView.getText());
> 
> // call superclass to save any view hierarchy
> 
> super.onSaveInstanceState(outState);
> 
> }

码 2‑11

除了定义 XML
文件，然后将其传递给 setContentView()，还可以在活动代码中新建 View 对象，并将新建的 View 插入到 ViewGroup 中，以构建视图层次结构。然后，将根 ViewGroup 传递给 setContentView() 以使用该布局。活动并不会一直处于“已创建”状态。onCreate() 方法完成执行后，活动进入“已开始”状态，系统会相继调用 onStart() 和 onResume() 方法。下一部分将介绍 onStart() 回调。

  - onStart()

当活动进入“已开始”状态时，系统会调用此回调。onStart() 调用使活动对用户可见，因为应用会为活动进入前台并支持互动做准备，例如应用通过此方法来初始化维护界面的代码。当活动进入已开始状态时，与活动生命周期相关联的所有生命周期感知型组件都将收到 ON\_START 事件。onStart() 方法会非常快速地完成，并且与“已创建”状态一样，活动不会一直处于“已开始”状态。一旦此回调结束，活动便会进入“已恢复”状态，系统将调用 onResume() 方法。

  - onResume()

活动会在进入“已恢复”状态时来到前台，然后系统调用 onResume() 回调。这是应用与用户互动的状态。应用会一直保持这种状态，直到某些事件发生，让焦点远离应用。此类事件包括接到来电、用户导航到另一个活动，或设备屏幕关闭。当活动进入已恢复状态时，与活动生命周期相关联的所有生命周期感知型组件都将收到 ON\_RESUME 事件。这时生命周期组件可以启用在组件可见且位于前台时需要运行的任何功能，例如启动相机预览。当发生中断事件时，活动进入“已暂停”状态，系统调用 onPause() 回调。如果活动从“已暂停”状态返回“已恢复”状态，系统将再次调用 onResume() 方法。因此，应实现 onResume()，以初始化在 onPause() 期间释放的组件，并执行每次活动进入“已恢复”状态时必须完成的任何其他初始化操作。以下是生命周期感知型组件的示例，该组件在收到 ON\_RESUME 事件时访问相机：

> public class CameraComponent implements LifecycleObserver {
> 
> ...
> 
> @OnLifecycleEvent(Lifecycle.Event.ON\_RESUME)
> 
> public void initializeCamera() {
> 
> if (camera == null) {
> 
> getCamera();
> 
> }
> 
> }
> 
> ...
> 
> }

码 2‑12

LifecycleObserver 收到 ON\_RESUME
事件后，上述代码便会初始化相机。然而，在多窗口模式下，即使处于“已暂停”状态，活动也可能完全可见。例如，当用户处于多窗口模式，并点按另一个不包含活动的窗口时，活动将进入“已暂停”状态。如果希望相机仅在应用处于“已恢复”（可见且在前台运行）状态时可用，在收到上述
ON\_RESUME 事件后初始化相机。如果希望在活动处于“已暂停”状态但可见时（例如在多窗口模式下）保持相机可用，应在收到 ON\_START
事件后初始化相机。但注意，若要让相机在活动处于“已暂停”状态时可用，可能会导致系统在多窗口模式下拒绝其他处于“已恢复”状态的应用访问相机。有时可能有必要让相机在活动处于“已暂停”状态时保持可用，但这样做实际可能会降低整体用户体验，仔细考虑生命周期的哪个阶段更适合在多窗口环境下控制共享系统资源。

无论选择在哪个构建事件中执行初始化操作，都请务必使用相应的生命周期事件来释放资源。如果在收到 ON\_START 事件后初始化某些内容，在收到
ON\_STOP 事件后释放或终止相应内容。如果在收到 ON\_RESUME 事件后初始化某些内容，在收到 ON\_PAUSE 事件后将其释放。

请注意，上述代码段将相机初始化代码放置在生命周期感知型组件中，也可以直接将此代码放入活动生命周期回调（例如 onStart() 和 onStop()），但不建议这样做。通过将此逻辑添加到独立的生命周期感知型组件中，可以对多个活动重复使用该组件，而无需复制代码。

#### 暂停状态

如果一个Activity暂时不被用户进入后端，例如当用户点按“返回”或“最近使用的应用”按钮时，就会出现此状态。从技术上来说，这意味着这个Activity
仍然部分可见，但大多数情况下，这表明用户正在离开该活动，该活动很快将进入“已停止”或“已恢复”状态。一个暂停状态的Activity依然可以重启运行，系统内存保持所有的状态，成员信息，和窗口管理器保持连接，但是在系统内存极端低下的时候将被杀掉。

  - onPause()

系统将此方法视为用户将要离开Activity 的第一个标志，尽管这并不总是意味着
Activity会被销毁。此方法表示Activity不再位于前台，尽管在用户处于多窗口模式时活动仍然可见。使用 onPause() 方法暂停或调整当活动处于“已暂停”状态时不应继续（或应有节制地继续）的操作，以及您希望很快恢复的操作。活动进入此状态的原因有很多。例如：

  - 如 onResume() 部分所述，某个事件会中断应用执行，这是最常见的情况。

  - 在 Android 7.0（API 级别
    24）或更高版本中，有多个应用在多窗口模式下运行。无论何时，都只有一个应用（窗口）可以拥有焦点，因此系统会暂停所有其他应用。

  - 有新的半透明活动（例如对话框）处于开启状态，只要活动仍然部分可见但并未处于焦点之中，便会一直暂停。

当活动进入已暂停状态时，与活动生命周期相关联的所有生命周期感知型组件都将收到 ON\_PAUSE 事件。这时，生命周期组件可以停止在组件未位于前台时无需运行的任何功能，例如停止相机预览。还可以使用 onPause() 方法释放系统资源、传感器（例如
GPS）手柄，或当Activity 暂停且用户不需要它们时仍然可能影响电池续航时间的任何资源。然而正如上文的 onResume()
部分所述，如果处于多窗口模式，已暂停的活动仍完全可见。因此，应该考虑使用
onStop()而非onPause()来完全释放或调整与界面相关的资源和操作，以便更好地支持多窗口模式。响应
ON\_PAUSE 事件的以下 LifecycleObserver 示例与上述 ON\_RESUME 事件示例相对应，会释放在收到
ON\_RESUME 事件后初始化的相机：

> public class JavaCameraComponent implements LifecycleObserver {
> 
> ...
> 
> @OnLifecycleEvent(Lifecycle.Event.ON\_PAUSE)
> 
> public void releaseCamera() {
> 
> if (camera \!= null) {
> 
> camera.release();
> 
> camera = null;
> 
> }
> 
> }
> 
> ...
> 
> }

码 2‑13

请注意，上述代码段在 LifecycleObserver 收到 ON\_PAUSE 事件后放置相机释放代码。

onPause() 执行非常简单，而且不一定要有足够的时间来执行保存操作。因此，您不应使用 onPause() 来保存应用或用户数据、进行网络调用或执行数据库事务。因为在该方法完成之前，此类工作可能无法完成。相反，您应在 onStop() 期间执行高负载的关闭操作。onPause() 方法的完成并不意味着活动离开“已暂停”状态。相反，活动会保持此状态，直到其恢复或变成对用户完全不可见。如果活动恢复，系统将再次调用 onResume() 回调。如果活动从“已暂停”状态返回“已恢复”状态，系统会让活动实例继续驻留在内存中，并会在系统调用 onResume() 时重新调用该实例。在这种情况下，您无需重新初始化在任何回调方法导致活动进入“已恢复”状态期间创建的组件。如果活动变为完全不可见，系统会调用 onStop()。

#### 停止状态

如果一个活动被另外的活动完全覆盖掉，叫做停止状态，其依然保持所有状态和成员信息，但是其不再可见，所以窗口被隐藏，当系统内存需要被用在其他地方的时候，停止的活动将被杀掉。

  - onStop()

如果您的活动不再对用户可见，说明其已进入“已停止”状态，因此系统将调用 onStop()
回调。例如，当新启动的活动覆盖整个屏幕时，可能会发生这种情况。如果活动已结束运行并即将终止，系统还可以调用
onStop()。当活动进入已停止状态时，与活动生命周期相关联的所有生命周期感知型组件都将收到 ON\_STOP
事件。这时，生命周期组件可以停止在组件未显示在屏幕上时无需运行的任何功能。在
onStop() 方法中，应用应释放或调整在应用对用户不可见时的无用资源。例如，应用可以暂停动画效果，或从精确位置更新切换到粗略位置更新。使用
onStop() 而非 onPause() 可确保与界面相关的工作继续进行，即使用户在多窗口模式下查看活动也能如此。

还应使用 onStop() 执行 CPU 相对密集的关闭操作。例如，如果您无法找到更合适的时机来将信息保存到数据库，可以在 onStop()
期间执行此操作。以下示例展示了 onStop() 的实现，它将草稿笔记内容保存到持久性存储空间中：

> @Override
> 
> protected void onStop() {
> 
> // call the superclass method first
> 
> super.onStop();
> 
> // save the note's current draft, because the activity is stopping
> 
> // and we want to be sure the current note progress isn't lost.
> 
> ContentValues values = new ContentValues();
> 
> values.put(NotePad.Notes.COLUMN\_NAME\_NOTE, getCurrentNoteText());
> 
> values.put(NotePad.Notes.COLUMN\_NAME\_TITLE, getCurrentNoteTitle());
> 
> // do this update in background on an AsyncQueryHandler or equivalent
> 
> asyncQueryHandler.startUpdate (
> 
> mToken, // int token to correlate calls
> 
> null, // cookie, not used here
> 
> uri, // The URI for the note to update.
> 
> values, // The map of column names and new values to apply to them.
> 
> null, // No SELECT criteria are used.
> 
> null // No WHERE columns are used.
> 
> );
> 
> }

码 2‑14

请注意，上述代码示例直接使用 SQLite，但实际应用中改用 Room，这是一个通过 SQLite 提供抽象层的持久性库。

当活动进入“已停止”状态时，活动对象会继续驻留在内存中：该对象将维护所有状态和成员信息，但不会附加到窗口管理器。活动恢复后，活动会重新调用这些信息。无需重新初始化在任何回调方法导致活动进入“已恢复”状态期间创建的组件。系统还会追踪布局中每个
View 对象的当前状态，如果用户在 EditText 微件中输入文本，系统将保留文本内容，因此您无需保存和恢复文本。

注意：活动停止后，如果系统需要恢复内存，可能会销毁包含该活动的进程。即使系统在活动停止后销毁相应进程，系统仍会保留 Bundle（键值对的
blob）中 View 对象（例如 EditText 微件中的文本）的状态，并在用户返回活动时恢复这些对象。

进入“已停止”状态后，活动要么返回与用户互动，要么结束运行并消失。如果活动返回，系统将调用 onRestart()。如果活动结束运行，系统将调用
onDestroy()。下一部分将介绍 onDestroy() 回调。

#### 销毁状态

如果一个活动是暂停或者停止状态，系统可以将该活动从内存中删除，安卓系统采用两种方式进行删除，要么要求该Activity结束，要么直接杀掉其的进程。

  - onDestroy()

当该Activity再次显示给用户时，其必须重新开始和重置前面的状态。 销毁活动之前，系统会先调用
onDestroy()。系统调用此回调的原因如下：

  - 由于用户彻底关闭活动或系统为活动调用 finish()，活动即将结束

  - 由于配置变更（例如设备旋转或多窗口模式），系统暂时销毁活动

当活动进入已销毁状态时，与活动生命周期相关联的所有生命周期感知型组件都将收到 ON\_DESTROY
事件。这时，生命周期组件可以在活动被销毁之前清理所需的任何数据。

应该使用 ViewModel 对象来包含活动的相关视图数据，而不是在Activity
中加入逻辑来确定活动被销毁的原因。如果因配置变更而重新创建活动，ViewModel
不必执行任何操作，因为系统将保留 ViewModel 并将其提供给下一个活动实例。如果不重新创建活动，ViewModel 将调用
onCleared() 方法，以便在活动被销毁前清除所需的任何数据，可以使用 isFinishing() 方法区分这两种情况。

如果活动即将结束，onDestroy() 是活动收到的最后一个生命周期回调。如果由于配置变更而调用
onDestroy()，系统会立即新建活动实例，然后在新配置中为新实例调用
onCreate()。onDestroy() 回调应释放先前的回调（例如 onStop()）尚未释放的所有资源。

#### 状态转换

活动的生命周期中有三个关键的循环，见图 2‑19：

  - 整个的生命周期

从onCreate()开始到onDestroy()结束。活动在onCreate()设置所有的“全局”状态，在onDestory()释放所有的资源。例如：某个活动有一个在后台运行的线程，用于从网络下载数据，则该活动可以在onCreate()中创建线程，在onDestory()中停止线程。

  - 可见的生命周期

从onStart()开始到onStop()结束。在这段时间，可以看到活动在屏幕上，尽管有可能不在前台，不能和用户交互。在这两个接口之间，需要保持显示给用户的界面数据和资源等，例如可以在onStart中注册一个IntentReceiver来监听数据变化导致界面的变动，当不再需要显示时候，可以在onStop()中注销。onStart()，onStop()都可以被多次调用，因为活动随时可以在可见和隐藏之间转换。

  - 前台的生命周期

从onResume()开始到onPause()结束。在这段时间里，该活动处于所有活动的最前面，和用户进行交互。活动可以经常性地在resumed和paused状态之间切换，例如：当设备准备休眠时，当一个活动处理结果被分发时，当一个新的Intent被分发时。所以在这些接口方法中的代码应该属于非常轻量级的。

![](md/media/chapter02_ui_design_basics/media/image44.png)

图 2‑19活动的状态转换

活动的整个生命周期的状态转换和动作都定义在的回调方法中，所有方法都可以被重写，见码 2‑15

> public class活动extends ApplicationContext {
> 
> protected void onCreate(Bundle icicle);
> 
> protected void onStart();
> 
> protected void onRestart();
> 
> protected void onResume();
> 
> protected void onPause();
> 
> protected void onStop();
> 
> protected void onDestroy();
> 
> }

码 2‑15活动**的回调方法**

下面继续上一小节的例子，在MainActivity中重写活动所有的回调方法，在ScrollView中的TextView内显示自己在各种状态转换时，调用了哪些不同回调方法。然后可以根据所调用的回调方法，参考**图
2‑19**，确认活动在哪些状态之间进行了转换，何时是何种状态，码 2‑16是MainActivity最终完整的代码。

> package com.example.ch02.lifecycle;
> 
> import android.os.Bundle;
> 
> import android.util.Log;
> 
> import androidx.appcompat.app.AppCompatActivity;
> 
> public class MainActivity extends AppCompatActivity {
> 
> private static final String ACTIVITY\_TAG =
> MainActivity.class.getSimpleName();
> 
> private void showLog(String text) {
> 
> Log.d(ACTIVITY\_TAG, text);
> 
> }
> 
> @Override
> 
> public void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> showLog("活动Created");
> 
> }
> 
> @Override
> 
> protected void onRestart() {
> 
> super.onRestart();//call to restart after onStop
> 
> showLog("活动restarted");
> 
> }
> 
> @Override
> 
> protected void onStart() {
> 
> super.onStart();//soon be visible
> 
> showLog("活动started");
> 
> }
> 
> @Override
> 
> protected void onResume() {
> 
> super.onResume();//visible
> 
> showLog("活动resumed");
> 
> }
> 
> @Override
> 
> protected void onPause() {
> 
> super.onPause();//invisible
> 
> showLog("活动paused");
> 
> }
> 
> @Override
> 
> protected void onStop() {
> 
> super.onStop();
> 
> showLog("活动stopped");
> 
> }
> 
> @Override
> 
> protected void onDestroy() {
> 
> super.onDestroy();
> 
> showLog("活动is being destroyed");
> 
> }
> 
> }

码 2‑16 MainActivity.java

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<manifest xmlns:android="http://schemas.android.com/apk/res/android"
> 
> package="com.example.ch02.lifecycle"\>
> 
> \<application
> 
> android:allowBackup="true"
> 
> android:icon="@mipmap/ic\_launcher"
> 
> android:label="@string/app\_name"
> 
> android:roundIcon="@mipmap/ic\_launcher\_round"
> 
> android:supportsRtl="true"
> 
> android:theme="@style/Theme.LifeCyscle"\>
> 
> \<activity
> 
> android:name=".MainActivity"
> 
> android:exported="true"\>
> 
> \<intent-filter\>
> 
> \<action android:name="android.intent.action.MAIN" /\>
> 
> \<category android:name="android.intent.category.LAUNCHER" /\>
> 
> \</intent-filter\>
> 
> \</activity\>
> 
> \</application\>
> 
> \</manifest\>

码 2‑17

（1）当运行上述程序时，会注意到模拟器中会打开一个空白的白色屏幕，实际上已经通过重写onCreate()方法来移除它。现在打开Android
Monitor中的Logcat，向上下滚动可以看到很多输出日志，在图
2‑20中标记1的位置输入“MainActivity”搜索特定的输出，会注意到三个方法被调用：活动Created、活动started
和活动resumed。

![](md/media/chapter02_ui_design_basics/media/image45.png)

图 2‑20 搜索特定的Logcat输出日志

（2）现在按下模拟器上的返回按钮并退出应用程序，点击图 2‑21中标记1的按钮，再次查看Logcat将看到另外两个方法被调用的输出（图
2‑22）：活动paused、活动stopped。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image46.png" style="width:1.87313in;height:1.81104in" /></p>
<p>图 2‑21 三种模拟器操作</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image47.png" style="width:3.64272in;height:0.85075in" /></p>
<p>图 2‑22 点击退出按钮后的日志输出</p></td>
</tr>
</tbody>
</table>

(3)执行上述操作后，模拟器回到Home界面（图 2‑23），使用主鼠标按钮点击模拟器的中间，然后向上滑动展开安装的应用（图
2‑24），松开鼠标后点击“Life Cycle”应用，再次查看Logcat将看到新增三个方法被调用的输出（图
2‑25）：活动restarted、活动started、活动resumed。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image48.png" style="width:1.16452in;height:1.88624in" /></p>
<p>图 2‑23</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image49.png" style="width:1.34362in;height:1.86836in" /></p>
<p>图 2‑24</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image50.png" style="width:2.752in;height:0.8209in" /></p>
<p>图 2‑25</p></td>
</tr>
</tbody>
</table>

(4)现在点击图 2‑21中标记2的按钮，再次查看Logcat将看到新增三个方法被调用的输出：活动paused、活动stopped。

(5)如果要将这个应用从后台调入到前端，点击图 2‑21中标记3的按钮，后台的应用浮动左右浏览选择（图 2‑26），鼠标点击“Life
Cycle”应用；如果要彻底销毁“Life Cycle”应用，使用主鼠标键选择“Life Cycle”应用并按住向上滑动（图
2‑26），再次查看Logcat这两个操作的调用输出为（图
2‑27）：活动restarted、活动started、活动resumed，活动paused，活动stopped，活动is
being destroyed。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image51.png" style="width:1.66548in;height:2.64179in" /></p>
<p>图 2‑26</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image52.png" style="width:3.37347in;height:0.76119in" /></p>
<p>图 2‑27</p></td>
</tr>
</tbody>
</table>

### 任务和回退栈

一个应用程序通常包含多个活动，每个活动都可以设计完成特定的用户操作，并且能够启动其他活动，例如一个电子邮件的应用程序可能有一个活动，用于展现出新的电子邮件列表，当用户选择了一个电子邮件，就打开一个新的活动以查看该电子邮件的详细内容。

一个活动也可以启动设备上的另一应用程序中的活动，例如如果我们的应用程序想要发送一个电子邮件，我们可以把邮件地址和内容等信息打包在一个叫意图的组件中，设置启动电子邮件应用程序的“创建邮件”活动，并获取意图中传递的信息。当邮件被发送后，活动则重新展现，而用户的感觉是发送邮件的功能好像是应用程序的一部分。虽然上述完成的动作是来自不同的应用程序，但是安卓系统将这些活动放入到相同的任务中，这样就维护了一个完整的用户体验。

所谓任务就是某些参与用户交互的活动集合，其目的是为完成某项确定的工作。安卓系统通过任务栈结构来管理任务中的这些活动，活动按照被打开的顺序排列在任务栈中。设备的Home屏幕是大多数任务的起点，当用户触摸应用程序的图标（或者Home屏幕上的快捷方式）时，该应用程序的任务就会来到前台。如果该应用的任务不存在（即应用在最近时间段内没有使用过），那么一个新的任务被创建，应用的主活动会作为任务栈中的根活动打开。如果用户从当前的Activity\_1打开了一个新的Activity\_2，则新的Activity\_2被压入到栈的顶部并且成为用户的前端界面，而原来的Activity\_1仍然在任务栈中，但是已经变成停止状态，此时系统会保留其用户界面的状态，继续打开新的Activity\_3成为用户的前端界面，活动\_1和Activity\_2依次仍然在任务栈中，此时系统会保留它们用户界面的状态。当用户按下返回按钮时，当前的Activity\_3就会从任务栈的顶部弹出，即当前的Activity\_3就会被销毁，而原来的Activity\_2就会被重新恢复显示被系统保存的状态。在任务栈中的活动不会被重排，只有压入和弹出两种操作，这种任务栈的读写方式为“后进先出”，称其为回退栈。**图
2‑28**展示了多个活动在一个任务中切换的过程。

![](md/media/chapter02_ui_design_basics/media/image53.png)

**图** **2‑28活动的回退栈**

如果用户不停地按返回键的时候，那么回退栈中每个活动都会依次弹出，并显示之前的活动，直至用户回到Home屏幕（或者当任务启动时的任何一个活动）。当所有的活动都从回退栈中弹出后，这个任务就不再存在。

一个任务就是一个完整的单元，当用户启动一个新的任务时或者使用Home按钮回到Home屏幕的时候，这个任务就会转变为后台。当任务处于后台时，里面所有的活动都处于停止状态，但是这个任务的回退栈仍然被完整保留。当其他任务变成前台时，当前的任务就变成后台，见**图
2‑29**。这样一来，任务可以回到用户前端，以便用户继续之前的操作。举例来说，假设当前任务
A的堆栈中有Activity\_Y和Activity\_X，用户按主屏幕按钮，然后从应用启动器中启动新应用，主屏幕出现后任务A转到后台。当新应用启动时，系统会启动该应用的任务B，该任务具有自己的活动\_Z
堆栈。与该应用互动后，用户再次返回到主屏幕并选择最初启动任务A的应用，现在任务A进入前台，其堆栈中的两个活动都完好如初，堆栈顶部的Activity\_Y恢复运行。此时，用户仍可通过以下方式切换到任务B：转到主屏幕并选择启动该任务的应用图标（或者从最近使用的应用屏幕中选择该应用的任务），这就是在安卓平台上进行多任务处理的一个例子。

![](md/media/chapter02_ui_design_basics/media/image54.png)

**图** **2‑29 两个任务：任务 B 在前台接收用户互动，任务 A 在后台等待恢复。**

虽然安卓系统可以在后台同时保留多个任务，但是假如用户同时运行着多个后台任务时，系统可能会销毁后台活动用于释放内存，这样的情况就会导致活动状态的丢失。

## 理解片段

安卓运行在各种各样的设备中，有各种尺寸的手机、各种标准的平板电脑、类型的控制屏幕甚至电视，而且在使用移动设备时，用户还会经常转换屏幕的纵横角度。虽然通过可以提供不同尺寸的位图、声明多种屏幕尺寸来解决分辨率不同的问题，但如果要应用的界面友好美观，还需要做大部分工作来为同一界面设计多个布局实现多屏幕适应。

在开发过程中，一般都是先基于手机开发一套应用，然后拷贝一份修改布局以适应什么超级大屏的。难道无法做到一种界面的布局可以同时适应手机和平板吗？在手机的界面布局设计过程中，主要是定义其中View和ViewGroup对象的层级结构。如果把这些结构模块化，就可以直接在屏幕转换角度或平板电脑等大屏幕的布局上使用了，片段（Fragment）出现的初衷就是为了解决这样的问题。片段是活动中用户界面的一部分，片段是一种灵活的、可重用界面组件，可以把一个片段看成是活动的一个布局模块，可以容纳具有某种层次结构的View和ViewGroup对象。

安卓系统从Android 3.0（API level
11）开始推出片段，主要目的是支持多屏幕更加动态和灵活的界面设计。用户界面设计时，把一个活动切分成多个片段，那么在把应用从手机屏幕迁移到大屏幕时，布局设计就不再需要改变和管理片段内部的视图层次结构，而可以重点考虑活动的显示外观了。

![](md/media/chapter02_ui_design_basics/media/image55.png)

**图** **2‑30片段的使用实例**

例如一个应用在手机中使用活动A显示文章列表，活动B显示列表中对应的文章内容。当用户点击活动A中文章列表的一项时，活动A启动活动B，在活动B中显示选中的文章内容，见图
2.30右图。如果每一个活动都使用一个片段来容纳界面上的视图对象，在设计这个应用在平板电脑的布局时，就可以不必考虑具体每一个图形控件的排列位置和关系，只需要考虑屏幕的容纳空间，适合排列那些片段；考虑用户操作时的友好性，这里在一个活动中同时并排显示两个片段，见图
2.30左图。如果用户点击左边片段的文章列表中的一项，选中的文章内容直接显示在右边的片段B中，不需要在启动另一个活动，也使应用的显示界面更友好美观。活动中的每一个片段都是Fragment的子类。在定义片段时，除了可以直接使用Fragment，还有一些子类可以使用，例如DialogFragment，直接定义浮动显示的对话框；ListFragment，已经定义好了一个列表的显示；PreferenceFragment，可以把偏好对象的系列显示成一个列表。

在进行界面布局设计时，考虑把每一个片段都设计成活动的可重用组件，这样一个设计好的片段不仅可以在多种屏幕设计时使用，也可以由同一设备的多个活动使用。在构建活动时，既可以静态使用多个片段，也可以在活动运行时，根据用户的交互情况对片段进行添加、移除、替换以及执行其他动作。

### 生命周期

片段必须放置在活动中使用，不能独立存在。片段具有自己的生命周期，但它的生命周期也直接受所在的活动生命周期的影响，例如当一个活动被销毁时，所包含的所有片段也都被销毁了。一个活动中可以包含多个片段，每个片段都定义自己的布局，并在生命周期中的回调方法中定义自己的动作。片段之间是相互独立的，活动通过回退栈来管理每个片段发生的事件，每一个对片段操作的事件，都会添加到回退栈中，通过回退栈允许用户通过回退键取消已经执行的动作。

![](md/media/chapter02_ui_design_basics/media/image56.png)

图 2‑31 Fragment的生命周期

活动的生命周期，直接影响片段的生命周期。在活动状态转换时，生命周期中每一个回调方法的调用，都导致其中的片段对应的回调方法的调用。与活动类似，片段以三个状态存在：Resumed、Paused和Stopped，在状态之间转换时，系统会调用相应的回调方法，
片段状态之间转换时，会有多个事件发生，每一个事件都会有片段回调方法调用，这些事件对应的回调方法如下：

| 回调方法                  | 说明                             |
| --------------------- | ------------------------------ |
| onAttach()            | 在片段对象添加到活动中时调用。                |
| onCreate()            | 创建片段对象时调用                      |
| onCreateView()        | 在片段绘制用户界面时调用                   |
| onActivityCreated()   | 在活动和片段的界面创建时调用。                |
| onStart()             | 在任何界面变化时，片段开始变为可视状态时调用         |
| onResume()            | 在片段开始变为运行状态时调用                 |
| onPause()             | 在片段运行状态结束，线程挂起，所在活动不再是前台界面时调用。 |
| onSaveInstanceState() | 在片段运行状态结束，保存界面状态时调用时。          |
| onStop()              | 在可见状态结束时调用                     |
| onDestroyView()       | 在片段视图被删除时调用                    |
| onDestroy()           | 在片段生命周期结束时调用                   |
| onDetach()            | 当片段被所在活动删除时调用。                 |

表 2‑3片段回调方法

### 创建片段

片段需要对AndroidX Fragment库的依赖 ，需要将Google Maven
存储库添加到项目settings.gradle文件中才能包含此依赖项。

> dependencyResolutionManagement {
> 
> repositoriesMode.set(RepositoriesMode.FAIL\_ON\_PROJECT\_REPOS)
> 
> repositories {
> 
> google()
> 
> ...
> 
> }
> 
> }

码 2‑18

要将AndroidX片段库包含到项目中，在应用build.gradle文件中添加以下依赖项：

> dependencies {
> 
> def fragment\_version = "1.4.0"
> 
> // Java language implementation
> 
> implementation "androidx.fragment:fragment:$fragment\_version"
> 
> // Kotlin
> 
> implementation "androidx.fragment:fragment-ktx:$fragment\_version"
> 
> }

码 2‑19

如果要创建片段，需要继承AndroidX Fragment类，并覆盖其方法以插入应用程序逻辑，类似于创建Activity类的方式。要创建定义其自己布局的最小片段，将片段的布局资源提供给基本构造函数，如以下示例所示：

> class ExampleFragment extends Fragment{
> 
> public ExampleFragment() {
> 
> super(R.layout.example\_fragment);
> 
> }
> 
> }

码 2‑20

片段库还提供了更专业的基类：

  - DialogFragment

显示一个浮动对话框，使用此类创建对话框是在 Activity类中使用对话框帮助器方法的一个很好的替代方法，因为片段会自动处理对话框。

  - PreferenceFragmentCompat

将对象的层次结构显示设置为列表，可以使用PreferenceFragmentCompat为应用创建设置屏幕。

如果要将片段添加到活动中，通常片段必须嵌入到
AndroidX FragmentActivity中，才能为该活动的布局贡献一部分界面。FragmentActivity 是 AppCompatActivity的基类，因此如果已经是AppCompatActivity的子类，可以在应用程序中提供向后兼容性，则无需更改活动的基类。

可以通过在活动的布局文件中定义片段，或在活动的布局文件中定义片段容器，然后以编程方式从活动中添加片段，将片段添加到活动的视图层次结构中。无论哪种情况，都需要添加一个 FragmentContainerView 定义片段应放置在活动视图层次结构中的位置。强烈建议始终使用
FragmentContainerView作为片段的容器，因为 FragmentContainerView
位于androidx.fragment.app包下面，是专门为片段设计的自定义布局，扩展了FrameLayout，可以可靠地处理片段事务，并且还有其他功能来协调片段行为。如果通过
XML 添加片段，要以声明方式将片段添加到活动布局的
XML，使用 FragmentContainerView元素，这是一个包含单个FragmentContainerView的活动布局示例：

> \<\!-- res/layout/example\_activity.xml --\>
> 
> \<androidx.fragment.app.FragmentContainerView
> 
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> android:id="@+id/fragment\_container\_view"
> 
> android:layout\_width="match\_parent"
> 
> android:layout\_height="match\_parent"
> 
> android:name="com.example.ExampleFragment" /\>

码 2‑21

属性android:name指定要实例化的片段类名，当活动的布局加载时，指定的片段被实例化，onInflate() 在新实例化的片段上被调用，一个FragmentTransaction被创建，其将片段添加到FragmentManager。注意可以使用class属性作为另一种方式来指定片段要实例化的对象。如果以编程方式添加片段，要以编程方式将片段添加到活动的布局中，布局应包含FragmentContainerView用作片段容器，如以下示例所示：

> \<\!-- res/layout/example\_activity.xml --\>
> 
> \<androidx.fragment.app.FragmentContainerView
> 
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> android:id="@+id/fragment\_container\_view"
> 
> android:layout\_width="match\_parent"
> 
> android:layout\_height="match\_parent" /\>

码 2‑22

与 XML 方法不同，此处android:name不使用属性 FragmentContainerView，因此不会自动实例化特定的片段，
FragmentTransaction 用于实例化片段并将其添加到活动的布局中。当活动正在运行时，可以进行片段事务处理，例如添加、删除或替换片段。在FragmentActivity中，可以获得 FragmentManager的实例，该实例可用于创建FragmentTransaction，然后可以在活动的onCreate()方法中使用FragmentTransaction.add()
实例化片段，传入ViewGroup布局中容器的ID和要添加的片段类，然后提交事务，如下例所示：

> public class ExampleActivity extends AppCompatActivity {
> 
> public ExampleActivity() {
> 
> super(R.layout.example\_activity);
> 
> }
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> if (savedInstanceState == null) {
> 
> getSupportFragmentManager().beginTransaction()
> 
> .setReorderingAllowed(true)
> 
> .add(R.id.fragment\_container\_view, ExampleFragment.class, null)
> 
> .commit();
> 
> }
> 
> }
> 
> }

码 2‑23

在前面的示例中，请注意片段事务仅在savedInstanceStateis时创建null，这是为了确保在首次创建活动时只添加一次片段；当发生配置更改并重新创建活动时，savedInstanceState不再是null，并且不需要第二次添加片段，因为片段会自动从savedInstanceState恢复。如果片段需要一些初始数据，则可以在对FragmentTransaction.add()的调用中通过Bundle提供参数传递给片段，如下所示：

> public class ExampleActivity extends AppCompatActivity {
> 
> public ExampleActivity() {
> 
> super(R.layout.example\_activity);
> 
> }
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> if (savedInstanceState == null) {
> 
> Bundle bundle = new Bundle();
> 
> bundle.putInt("some\_int", 0);
> 
> getSupportFragmentManager().beginTransaction()
> 
> .setReorderingAllowed(true)
> 
> .add(R.id.fragment\_container\_view, ExampleFragment.class, bundle)
> 
> .commit();
> 
> }
> 
> }
> 
> }

码 2‑24

Bundle然后可以通过调用requireArguments()从片段中检索参数，并且可以使用适当的Bundlegetter方法来检索每个参数。

> class ExampleFragment extends Fragment{
> 
> public ExampleFragment() {
> 
> super(R.layout.example\_fragment);
> 
> }
> 
> @Override
> 
> public void onViewCreated(@NonNull View view, Bundle
> savedInstanceState) {
> 
> int someInt = requireArguments().getInt("some\_int");
> 
> ...
> 
> }
> 
> }

码 2‑25

### 管理片段

FragmentManager类负责对应用的片段执行一些操作，如添加、移除或替换，以及将其添加到返回堆栈。如果使用的是Jetpack
Navigation库，则可能永远不会直接与 FragmentManager交互，因为该库会代替使用
FragmentManager。也就是说，任何使用片段的应用都在某种程度上使用
FragmentManager，因此了解其是什么以及如何工作非常重要。本节介绍如何访问
FragmentManager、FragmentManager与活动和片段相关的角色、如何使用FragmentManager管理返回堆栈，以及如何为片段提供数据和依赖项。

如果在活动中访问 FragmentManager，每个 FragmentActivity 及其子类（如
AppCompatActivity）都可以通过getSupportFragmentManager()方法访问
FragmentManager。如果在片段中访问，片段也能够托管一个或多个子片段。在**片段**内，可以通过
getChildFragmentManager()方法获取对管理片段子级的FragmentManager的引用。如果需要访问其宿主FragmentManager，可以使用
getParentFragmentManager()方法。下面来看几个示例，看看片段、其宿主以及与每个片段关联的 FragmentManager
实例之间的关系。

![](md/media/chapter02_ui_design_basics/media/image57.png)

图 2‑32 两个界面布局示例，显示了片段与其宿主活动之间的关系

图
2‑32显示了两个示例，每个示例中都有一个活动宿主。这两个示例中的宿主活动都以BottomNavigationView的形式向用户显示顶级导航，该视图负责以应用中的不同屏幕换出宿主片段，其中每个屏幕都实现为单独的片段。例1中的宿主片段托管两个子片段，这些片段构成拆分视图屏幕，例2中的宿主片段托管一个子片段，该片段构成滑动视图的显示片段。

![](md/media/chapter02_ui_design_basics/media/image58.png)

图 2‑33 每个宿主都有与其关联的FragmentManager，用于管理其子片段

基于此设置，可以将每个宿主视为具有与其关联的 FragmentManager，用于管理其子片段。图 2‑33说明了这一点，并显示了
supportFragmentManager、parentFragmentManager 和 childFragmentManager
之间的属性映射。需要引用的相应 FragmentManager
属性取决于调用点在片段层次结构中的位置，以及尝试访问的片段管理器。对
FragmentManager 进行引用后，就可以使用其来操纵向用户显示的片段。

一般来说，应用由一个或少数几个活动组成，其中每个活动表示一组相关的屏幕。活动可能会提供一个点来放置顶级导航，并提供一个位置来限定ViewModels以及片段之间的其他视图状态的范围，应用中的每个目标点应由一个片段表示。如果想要一次显示多个片段，如在分割视图中或仪表盘内应使用子片段，由目标点片段及其子片段管理器进行管理。子片段的其他用例可能包括：

  - 屏幕滑动，其中父片段中的 ViewPager2 管理一系列子片段视图。

  - 一组相关屏幕中的子导航。

  - Jetpack Navigation将子片段用作各个目标点，一个活动托管一个父
    NavHostFragment，并在用户浏览应用时以不同的子目标的片段填充其空间。

FragmentManager管理片段返回堆栈。在运行时，FragmentManager
可以执行添加或移除片段等返回堆栈操作来响应用户互动。每一组更改作为一个单元一起提交，称为
FragmentTransaction。当用户按设备上的“返回”按钮时，或者当调用FragmentManager.popBackStack()
时，最上面的片段事务会从堆栈中弹出，换句话说事务的产生是反向的。如果堆栈上没有更多片段事务，并且没有使用子片段，则返回事件会向上传递到活动。当对事务调用
addToBackStack()时，请注意事务可以包括任意数量的操作，如添加多个片段、替换多个容器中的片段等等。回退栈被弹出时，所有这些操作会作为一项原子化操作反转。如果在调用popBackStack()之前提交了其他事务，并且没有对事务使用
addToBackStack()，则这些操作不会反转，因此在一个FragmentTransaction中，应避免让影响返回堆栈的事务与不影响返回堆栈的事务交织在一起。如需在布局容器中显示片段，使用FragmentManager
创建 FragmentTransaction。在事务中，随后可以对容器执行add()或replace()操作，例如一个简单的
FragmentTransaction可能如下所示：

> FragmentManager fragmentManager = getSupportFragmentManager();
> 
> fragmentManager.beginTransaction()
> 
> .replace(R.id.fragment\_container, ExampleFragment.class, null)
> 
> .setReorderingAllowed(true)
> 
> .addToBackStack("name") // name can be null
> 
> .commit();

码 2‑26

在本例中，ExampleFragment会替换当前在由 R.id.fragment\_container ID
标识的布局容器中的片段。将片段的类提供给replace()方法可让FragmentManager使用其
FragmentFactory处理实例化。setReorderingAllowed(true)
可优化事务中涉及的片段状态变化，以使动画和过渡正常运行。调用
addToBackStack()
会将事务提交到返回堆栈，用户稍后可以通过按“返回”按钮反转事务并恢复上一个片段。如果在一个事务中添加或移除了多个片段，弹出返回堆栈时，所有这些操作都会撤消。在addToBackStack()调用中提供的可选名称能够使用popBackStack()弹回到该特定事务。如果在执行移除片段的事务时未调用
addToBackStack()，则提交事务时会销毁已移除的片段，用户无法返回到该片段。如果在移除某个片段时调用了
addToBackStack()，则该片段只会被停止，稍后当用户返回时会重启。请注意，在这种情况下其视图会被销毁。可以使用
findFragmentById() 获取对布局容器中当前片段的引用。从 XML 扩充时，可使用 findFragmentById() 按给定的
ID 查找片段；在 FragmentTransaction 中添加时，可使用其按容器 ID 进行查找，示例如下：

> FragmentManager fragmentManager = getSupportFragmentManager();
> 
> fragmentManager.beginTransaction()
> 
> .replace(R.id.fragment\_container, ExampleFragment.class, null)
> 
> .setReorderingAllowed(true)
> 
> .addToBackStack(null)
> 
> .commit();
> 
> ...
> 
> ExampleFragment fragment =
> 
> (ExampleFragment)
> fragmentManager.findFragmentById(R.id.fragment\_container);

码 2‑27

或者也可以为片段分配一个唯一的标记，并使用 findFragmentByTag() 获取引用，可以在布局中定义的片段上使用
android:tag XML属性来分配标记，也可以在 FragmentTransaction中的 add() 或 replace()
操作期间分配标记。

> FragmentManager fragmentManager = getSupportFragmentManager();
> 
> fragmentManager.beginTransaction()
> 
> .replace(R.id.fragment\_container, ExampleFragment.class, null, "tag")
> 
> .setReorderingAllowed(true)
> 
> .addToBackStack(null)
> 
> .commit();
> 
> ...
> 
> ExampleFragment fragment = (ExampleFragment)
> fragmentManager.findFragmentByTag("tag");

码 2‑28

在任何给定的时间，只允许一个 FragmentManager
控制片段返回堆栈。如果应用在屏幕上同时显示多个同级片段，或者应用使用子片段，则必须指定一个
FragmentManager 来处理应用的主要导航。如需在片段事务内定义主要导航片段，对事务调用
setPrimaryNavigationFragment() 方法，并传入一个片段的实例，该片段的 childFragmentManager
应具有主要控制权。将导航结构视为一系列层，其中活动作为最外层，封装下面的每一层子片段。每一层都必须有一个主要导航片段。当发生返回事件时，最内层控制导航行为。一旦最内层再也没有可从其弹回的片段事务，控制权就会回到外面的下一层，此过程会一直重复，直至到达活动为止。请注意，当同时显示两个或更多片段时，其中只有一个可以是主要导航片段。如果将某个片段设为主要导航片段，会移除对先前片段的指定。在上例中，如果将详情片段设为主要导航片段，就会移除对主片段的指定。

在某些情况下，应用可能需要支持多个返回堆栈。一个常见示例是应用使用底部导航栏。FragmentManager 可通过
saveBackStack() 和 restoreBackStack()
方法支持多个返回堆栈，这两种方法使可以通过保存一个返回堆栈并恢复另一个返回堆栈来在返回堆栈之间进行交换。注意：或者也可以使用
NavigationUI 组件，该组件会自动处理对底部导航栏的多个返回堆栈支持。saveBackStack() 的工作方式类似于使用可选
name 参数调用 popBackStack()：弹出指定事务以及堆栈上在此之后的所有事务。不同之处在于 saveBackStack()
会保存弹出事务中所有片段的状态。假设之前使用 addToBackStack() 提交
FragmentTransaction，从而将片段添加到返回堆栈：

> supportFragmentManager.beginTransaction()
> 
> .replace(R.id.fragment\_container, ExampleFragment.class, null)
> 
> // setReorderingAllowed(true) and the optional string argument for
> 
> // addToBackStack() are both required if you want to use
> saveBackStack().
> 
> .setReorderingAllowed(true)
> 
> .addToBackStack("replacement")
> 
> .commit();

码 2‑29

在这种情况下，可以通过调用 saveState() 来保存此片段事务和 ExampleFragment 的状态：

> supportFragmentManager.saveBackStack("replacement");

码 2‑30

注意只能将 saveBackStack() 用于调用 setReorderingAllowed(true)
的事务，以确保可以将事务还原为单一原子操作。可以使用相同的名称参数调用
restoreBackStack()，以恢复所有弹出的事务以及所有保存的 fragment 状态：

> supportFragmentManager.restoreBackStack("replacement");

码 2‑31

注意除非使用 addToBackStack() 传递 fragment 事务的可选名称，否则不能使用 saveBackStack() 和
restoreBackStack()。添加片段时，可以手动实例化片段并将其添加到 FragmentTransaction。

> // Instantiate a new instance before adding
> 
> ExampleFragment myFragment = new ExampleFragment();
> 
> fragmentManager.beginTransaction()
> 
> .add(R.id.fragment\_view\_container, myFragment)
> 
> .setReorderingAllowed(true)
> 
> .commit();

码 2‑32

当提交片段事务时，创建的片段实例就是使用的实例。不过，在配置更改期间，活动及其所有片段都会被销毁，然后使用最适用的安卓资源重新创建。FragmentManager
会处理所有这些操作，重新创建片段的实例，将其附加到宿主，并重新创建返回堆栈状态。默认情况下，FragmentManager 使用框架提供的
FragmentFactory
实例化片段的新实例。此默认工厂使用反射来查找和调用片段的无参数构造函数。这意味着，无法使用此默认工厂为片段提供依赖项。这也意味着默认情况下在重新创建过程中，不会使用首次创建片段时所用的任何自定义构造函数。如需为片段提供依赖项或使用任何自定义构造函数，必须创建自定义
FragmentFactory 子类，然后替换 FragmentFactory.instantiate。随后可以将
FragmentManager 的默认工厂替换为您的自定义工厂，其随后用于实例化片段。

假设有一个 DessertsFragment，负责显示受欢迎的甜点，DessertsFragment 依赖于
DessertsRepository 类，该类可为其提供向用户显示正确界面所需的信息，可以将 DessertsFragment
定义为在其构造函数中需要 DessertsRepository 实例。

> public class DessertsFragment extends Fragment {
> 
> private DessertsRepository dessertsRepository;
> 
> public DessertsFragment(DessertsRepository dessertsRepository) {
> 
> super();
> 
> this.dessertsRepository = dessertsRepository;
> 
> }
> 
> // Getter omitted.
> 
> ...
> 
> }

码 2‑33

FragmentFactory 的简单实现可能与以下代码类似。

> public class MyFragmentFactory extends FragmentFactory {
> 
> private DessertsRepository repository;
> 
> public MyFragmentFactory(DessertsRepository repository) {
> 
> super();
> 
> this.repository = repository;
> 
> }
> 
> @NonNull
> 
> @Override
> 
> public Fragment instantiate(@NonNull ClassLoader classLoader, @NonNull
> String className) {
> 
> Class\<? extends Fragment\> fragmentClass =
> loadFragmentClass(classLoader, className);
> 
> if (fragmentClass == DessertsFragment.class) {
> 
> return new DessertsFragment(repository);
> 
> } else {
> 
> return super.instantiate(classLoader, className);
> 
> }
> 
> }
> 
> }

码 2‑34

此示例创建了 FragmentFactory 的子类，替换了 instantiate() 方法，以便为 DessertsFragment
提供自定义片段创建逻辑。其他片段类通过 super.instantiate() 由 FragmentFactory
的默认行为处理。随后可以通过在 FragmentManager 上设置一个属性，将
MyFragmentFactory 指定为要在构造应用的片段时使用的工厂。必须在活动的 super.onCreate()
之前设置此属性，以确保在重新创建片段时使用 MyFragmentFactory。

> public class MealActivity extends AppCompatActivity {
> 
> @Override
> 
> protected void onCreate(@Nullable Bundle savedInstanceState) {
> 
> DessertsRepository repository = DessertsRepository.getInstance();
> 
> getSupportFragmentManager().setFragmentFactory(new
> MyFragmentFactory(repository));
> 
> super.onCreate(savedInstanceState);
> 
> }
> 
> }

码 2‑35

注意在活动中设置 FragmentFactory 会替换整个活动的片段层次结构中的片段创建。换句话说，添加的任何子片段的
childFragmentManager 都会使用此处设置的自定义片段工厂，除非在较低的级别被替换。在一个活动架构中，应使用
FragmentScenario 类在隔离的条件下测试片段。由于无法依赖于活动的自定义 onCreate()逻辑，因此可以改为将
FragmentFactory 作为参数传入片段测试，如以下示例所示：

> // Inside your test
> 
> val dessertRepository = mock(DessertsRepository::class.java)
> 
> launchFragment\<DessertsFragment\>(factory =
> MyFragmentFactory(dessertRepository)).onFragment {
> 
> // Test Fragment logic
> 
> }

码 2‑36

### 事务处理

在运行时，FragmentManager 可以添加、删除、替换和使用片段执行其他操作以响应用户交互，提交的每组片段更改称为一个事务，可以使用
FragmentTransaction 该类提供的 API
指定在事务中执行的操作，可以将多个操作分组到一个事务中，例如一个事务可以添加或替换多个片段。当在同一屏幕上显示多个同级片段时，此分组可能很有用，例如拆分视图，可以将每个事务保存到由FragmentManager管理的后退堆栈，允许用户向后导航片段更改，类似于向后导航活动，可以通过调用获取FragmentTransaction的实例，如下例所示：

> FragmentManager fragmentManager = ...
> 
> FragmentTransaction fragmentTransaction =
> fragmentManager.beginTransaction();

码 2‑37

最终FragmentTransaction调用都必须提交事务，该commit()调用FragmentManager表示所有操作都已添加到事务中。

> FragmentManager fragmentManager = ...
> 
> FragmentTransaction fragmentTransaction =
> fragmentManager.beginTransaction();
> 
> // Add operations here
> 
> fragmentTransaction.commit();

码 2‑38

如果允许对片段状态更改重新排序，每个都FragmentTransaction应该使用setReorderingAllowed(true)：

> FragmentManager fragmentManager = ...
> 
> fragmentManager.beginTransaction()
> 
> ...
> 
> .setReorderingAllowed(true)
> 
> .commit();

码 2‑39

为了行为兼容性，默认情况下不启用重新排序标志，但是需要允许FragmentManager正确执行FragmentTransaction，特别是当其在后台堆栈上运行并运行动画和过渡时。启用该标志可确保如果多个事务一起执行，任何中间片段（即添加然后立即替换的片段）不会经历生命周期更改或执行其动画或转换，请注意此标志会影响事务的初始执行和使用popBackStack()撤销事务。

要将片段添加到FragmentManager，调用事务处理的add()方法，此方法接收片段容器的ID
，以及希望添加的片段类名，添加的片段被移动到重启状态，强烈建议容器是
FragmentContainerView
视图层次结构的一部分。调用remove()从宿主中删除片段，传入一个片段实例，该片段实例是通过findFragmentById()或findFragmentByTag()方法从片段管理器中检索到。如果片段的视图先前已添加到容器中，则此时视图将从容器中删除，移除的片段被移动为销毁状态。用replace()方法将容器中的现有片段替换为提供的新片段类实例，调用replace()相当于在容器中调用remove()方法，然后将新片段添加到同一个容器中，以下代码片段显示了如何将一个片段替换为另一个片段：

> // Create new fragment and transaction
> 
> FragmentManager fragmentManager = ...
> 
> FragmentTransaction transaction = fragmentManager.beginTransaction();
> 
> transaction.setReorderingAllowed(true);
> 
> // Replace whatever is in the fragment\_container view with this
> fragment
> 
> transaction.replace(R.id.fragment\_container, ExampleFragment.class,
> null);
> 
> // Commit the transaction
> 
> transaction.commit();

码 2‑40

在这个例子中，一个新的实例ExampleFragment替换了当前位于由R.id.fragment\_container标识的布局容器中的片段。注意强烈建议始终使用采用Class而不是片段实例的片段操作，以确保创建片段的相同机制也用于从保存状态恢复片段。默认情况下，在
FragmentTransaction中所做的更改不会添加到后台堆栈，要保存这些更改可以调用addToBackStack()。调用
commit() 不会立即执行事务，只要事务能够这样做是才被安排在主界面线程上运行，但是如有必要可以调用
commitNow()以立即在界面线程上运行片段事务。在FragmentTransaction中执行操作的顺序很重要，尤其是在使用setCustomAnimations()，此方法将给定的动画应用于其后的所有片段操作。

> getSupportFragmentManager().beginTransaction()
> 
> .setCustomAnimations(enter1, exit1, popEnter1, popExit1)
> 
> .add(R.id.container, ExampleFragment.class, null) // gets the first
> animations
> 
> .setCustomAnimations(enter2, exit2, popEnter2, popExit2)
> 
> .add(R.id.container, ExampleFragment.class, null) // gets the second
> animations
> 
> .commit()

码 2‑41

FragmentTransactions可以影响在事务范围内添加的各个片段的生命周期状态。创建 时 FragmentTransaction，
setMaxLifecycle() 为给定片段设置最大状态，例如ViewPager2用于
setMaxLifecycle()将屏幕外片段限制为启动状态。使用FragmentTransaction的show()和hide()方法显示和隐藏已添加到容器的片段视图，这些方法设置片段视图的可见性，而不影响片段的生命周期。虽然不需要使用片段事务来切换片段中视图的可见性，但这些方法对于希望更改可见性状态与后台堆栈上的事务相关联的情况很有用。FragmentTransaction的detach()方法将片段与界面分离，破坏其视图层次结构。片段保持与放入后堆栈时相同的停止状态，这意味着片段已从界面中删除，但仍由片段管理器管理。attach()方法重新附加之前分离的片段，这会导致其视图层次结构被重新创建、附加到界面并显示。

## 理解布局

前一节对活动有了初步的理解，也在具体的例子中运用到了活动的布局与控件。从继承的概念上来说，活动中具体用户图形界面的组件由安卓定义的View类和ViewGroup类的子类对象组成，称为View和ViewGroup对象。View对象是安卓平台上用户界面中的基础单元，也可用称为微件。安卓系统提供了许多类型的View，例如TextView和Button等类，这些都是View类的子类。ViewGroup对象可以理解为一种容器，类似于Java中的Panel，用于容纳其他的控件对象，并规定这些控件对象按照特定的规则进行排列，即按照某种层次结构排列。安卓系统也提供了许多类型的ViewGroup，例如ScrollView、RelativeLayout和TabHost等，这些都是ViewGroup的子类。

那么什么是布局呢？View和ViewGroup对象在活动中的排列层次结构，称为用户界面的布局。最常用的是线性布局LinearLayout，表格布局TableLayout、相对布局RelativeLayout、网页布局WebView和列表ListView等。在安卓平台，一个活动的用户界面能够使用层次关系的View和ViewGroup对象组合来设计布局。

### 布局概述

在第一章提到过，安卓系统实现活动的用户界面布局有两种定义方式：一种是使用XML文件定义布局，把布局文件置于/res/layout目录下；另一种是在JAVA应用程序中通过编程的方法来创建View和ViewGroup对象，在运行时实例化布局元素，或改变其属性。

安卓的XML布局资源文件主要用于活动用户界面或其他的用户界面组件的布局。使用XML布局文件的优势除了在前面的章节提到的之外，最重要的是可以将应用程序的界面设计与控制逻辑分离开来，这更有利于用户屏幕不确定的移动应用。如果需要调整界面设计，只需要修改XML文件，而无需修改源代码并重新编译，例如对于不同移动设备或用户，不同的屏幕方向，不同的屏幕尺寸，不同的语言等等，我们可以设计不同XML布局文件，但是可能并不需要修改任何应用程序代码。此外对于一个初学者来说，使用XML布局更容易定义用户界面的结构，更容易进行调试。这一节系统地介绍如何在XML布局文件中的XML语言，来设计和描述用户图形界面。XML布局资源文件的具体语法结构见码
2‑42。

<table>
<tbody>
<tr class="odd">
<td><blockquote>
<p>&lt;?xml version="1.0" encoding="utf-8"?&gt;</p>
<p>&lt;ViewGroup xmlns:android="http://schemas.android.com/apk/res/android"</p>
<p><strong>android:</strong>id="@[+][package:]id/resource_name"</p>
<p><strong>android:</strong>layout_height=["dimension" | "fill_parent" | "wrap_content"]</p>
<p><strong>android:</strong>layout_width=["dimension" | "fill_parent" | "wrap_content"]</p>
<p>[ViewGroup-specific attributes] &gt;</p>
<p>&lt;View</p>
<p><strong>android:</strong>id="@[+][package:]id/resource_name"</p>
<p><strong>android:</strong>layout_height=["dimension" | "fill_parent" | "wrap_content"]</p>
<p><strong>android:</strong>layout_width=["dimension" | "fill_parent" | "wrap_content"]</p>
<p>[View-specific attributes] &gt;</p>
<p>&lt;requestFocus/&gt;</p>
<p>&lt;/View&gt;</p>
<p>&lt;ViewGroup &gt;</p>
<p>&lt;View /&gt;</p>
<p>&lt;/ViewGroup&gt;</p>
<p>&lt;include layout="@layout/layout_resource"/&gt;</p>
<p>&lt;/ViewGroup&gt;</p>
</blockquote></td>
</tr>
</tbody>
</table>

码 2‑42 布局资源文件的语法

当编译应用时，系统会将每个 XML 布局文件编译成 View 资源，应在活动.onCreate() 回调实现内加载应用代码中的布局资源，通过调用
setContentView()，并以 R.layout.layout\_file\_name
形式向应用代码传递对布局资源的引用，即可执行此操作，例如如果
XML 布局保存为 main\_layout.xml，应通过如下方式为活动加载布局资源：

> public void onCreate(Bundle savedInstanceState) {
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.main\_layout);
> 
> }

码 2‑43

启动活动时，Android 框架会调用活动中的 onCreate() 回调方法。每个 View 对象和 ViewGroup对象均支持自己的各种
XML 属性。某些属性是 View 对象的特有属性（例如TextView支持textSize属性），但可扩展此类的任一 View
对象也会继承这些属性；某些属性是所有 View 对象的共有属性，因为这些继承自 View 根类，例如 id
属性。此外其他属性被视为布局参数，即描述 View 对象特定布局方向的属性，如该对象的父ViewGroup对象所定义的属性。任何
View 对象均可拥有与之关联的整型 ID，用于在结构树中对 View 对象进行唯一标识。编译应用后，系统会以整型形式引用此 ID，但在布局
XML 文件中，系统通常会以字符串的形式在 id 属性中指定该 ID。这是所有 View 对象共有的 XML 属性（由 View
类定义），并且您会经常使用该属性。XML 标记内部的 ID 语法是：

> android:id="@+id/my\_button"

码 2‑44

字符串开头处的 @ 符号指示 XML 解析器应解析并展开 ID 字符串的其余部分，并将其标识为 ID 资源。加号 (+)
表示这是一个新的资源名称，必须创建该名称并将其添加到资源内（在 R.java
文件中）。安卓框架还提供许多其他 ID 资源。引用安卓资源 ID
时不需要加号，但必须添加“android”软件包命名空间，如下所示：

> android:id="@android:id/empty"

码 2‑45

添加 android 软件包命名空间后，现在将从 android.R 资源类而非本地资源类引用
ID。为了创建视图并从应用中引用它们，常见的模式是：

(1)在布局文件中定义视图/微件，并为其分配唯一 ID：

> \<Button android:id="@+id/my\_button"
> 
> android:layout\_width="wrap\_content"
> 
> android:layout\_height="wrap\_content"
> 
> android:text="@string/my\_button\_text"/\>

码 2‑46

(2)然后创建视图对象的实例，并从布局中捕获它，通常使用 onCreate() 方法：

> Button myButton = (Button) findViewById(R.id.my\_button);

码 2‑47

创建 RelativeLayout 时，请务必为视图对象定义ID。在相对布局中，同级视图可定义其相对于其他通过唯一ID引用的同级视图的布局。ID
无需在整个结构树中具有唯一性，但其在您要搜索的结构树部分中应具有唯一性（要搜索的部分往往是整个结构树，因此最好尽可能具有全局唯一性）。ID
无需在整个结构树中具有唯一性，但其在要搜索的结构树部分中应具有唯一性（要搜索的部分往往是整个结构树，因此最好尽可能具有全局唯一性）。

名为 layout\_something 的 XML 布局属性可以为视图定义适合其所在 ViewGroup 的布局参数。每个 ViewGroup
类都会实现一个扩展 ViewGroup.LayoutParams 的嵌套类。此子类包含的属性类型会根据需要为视图组的每个子视图定义尺寸和位置。如图
2‑35所示，父视图组会为每个子视图（包括子视图组）定义布局参数。

![](md/media/chapter02_ui_design_basics/media/image59.png)

图 2‑37 以可视化方式表示的视图层次结构，其中包含与每个视图关联的布局参数

请注意，每个 LayoutParams 子类都有自己的值设置语法。每个子元素都必须定义适合其父元素的
LayoutParams，但父元素也可为其子元素定义不同的
LayoutParams。所有视图组均包含宽度和高度（layout\_width 和 layout\_height），并且每个视图都必须定义它们。许多
LayoutParams
还包括可选的外边距和边框。可以指定具有确切尺寸的宽度和高度，但多半不想经常这样做，更常见的情况是会使用以下某种常量来设置宽度或高度：

  - wrap\_content 

指示视图将其大小调整为内容所需的尺寸。

  - match\_parent 

指示视图尽可能采用其父视图组所允许的最大尺寸。

一般而言，建议不要使用绝对单位（如像素）来指定布局宽度和高度。更好的方法是使用相对测量单位，如与密度无关的像素单位 dp、wrap\_content 或 match\_parent，因为这样有助于确保应用在各类尺寸的设备屏幕上正确显示。

视图的几何形状就是矩形的几何形状。视图拥有一个位置（以一对“水平向左”和“垂直向上”的坐标表示）和两个尺寸（以宽度和高度表示）。位置和尺寸的单位是像素。可以通过调用 getLeft() 方法和 getTop() 方法来检索视图的位置。前者会返回表示视图的矩形的水平向左（或称
X 轴）坐标；后者会返回表示视图的矩形的垂直向上（或称 Y 轴）坐标。这些方法都会返回视图相对于其父项的位置，例如如果 getLeft() 返回
20，则表示视图位于其直接父项左边缘向右 20
个像素处。此外，系统还提供了几种便捷方法来避免不必要的计算，即 getRight() 和 getBottom()，这些方法会返回表示视图的矩形的右边缘和下边缘的坐标，例如调用 getRight() 类似于进行以下计算：getLeft()
+ getWidth()。

视图尺寸通过宽度和高度表示，实际上视图拥有两对宽度和高度值。第一对称为测量宽度和测量高度。这些尺寸定义视图希望在其父项内具有的大小，可通过调用 getMeasuredWidth() 和 getMeasuredHeight() 来获得这些测量尺寸。第二对简称为宽度和高”，有时称为绘制宽度和绘制高度。这些尺寸定义绘制时和布局后，视图在屏幕上的实际尺寸。这些值可以（但不必）与测量宽度和测量高度不同，可通过调用 getWidth() 和 getHeight() 来获得宽度和高度。

为了测量尺寸，视图需将其内边距考虑在内。内边距以视图左侧、顶部、右侧和底部各部分的像素数表示。内边距可用于以特定数量的像素弥补视图内容，例如若左侧内边距为
2，则会将视图内容从左边缘向右推 2 个像素。可以使用 setPadding(int, int, int,
int) 方法设置内边距，并通过调用 getPaddingLeft()、getPaddingTop()、getPaddingRight() 和 getPaddingBottom() 查询内边距。尽管视图可以定义内边距，但它并不支持外边距，不过视图组可以提供此类支持。

ViewGroup
类的每个子类都会提供一种独特的方式，以显示您在其中嵌套的视图。以下是安卓平台中一些较为常见的内置布局类型。如果布局的内容是动态内容或未预先确定的内容，可以使用继承 AdapterView 的布局，在运行时用视图填充布局。AdapterView 类的子类会使用 Adapter 将数据与其布局绑定。Adapter 充当数据源与 AdapterView 布局之间的中间方：Adapter 会（从数组或数据库查询等来源）检索数据，并将每个条目转换为可添加到 AdapterView 布局中的视图。适配器支持的常见布局包括线性布局、相对布局和网页视图（表
2‑3）。

| 布局类型 | 样式                                                         | 说明                                                      |
| ---- | ---------------------------------------------------------- | ------------------------------------------------------- |
| 线性布局 | ![](md/media/chapter02_ui_design_basics/media/image60.png) | 一种使用单个水平行或垂直行来组织子项的布局。此布局会在窗口长度超出屏幕长度时创建滚动条             |
| 相对布局 | ![](md/media/chapter02_ui_design_basics/media/image61.png) | 能指定子对象彼此之间的相对位置（子对象 A 在子对象 B 左侧）或子对象与父对象的相对位置（与父对象顶部对齐） |
| 网页视图 | ![](md/media/chapter02_ui_design_basics/media/image62.png) | 显示网页                                                    |
| 列表视图 | ![](md/media/chapter02_ui_design_basics/media/image63.png) | 显示滚动的单列列表                                               |
| 网格视图 | ![](md/media/chapter02_ui_design_basics/media/image64.png) | 显示滚动的行列网格                                               |

表 2‑4 常见布局类型

如果布局的内容是动态内容或未预先确定的内容，您可以使用继承 AdapterView 的布局，在运行时用视图填充布局。AdapterView
类的子类会使用 Adapter 将数据与其布局绑定。Adapter 充当数据源与 AdapterView
布局之间的中间方：Adapter 会（从数组或数据库查询等来源）检索数据，并将每个条目转换为可添加到
AdapterView 布局中的视图，适配器支持的常见布局包括列表视图和网格视图（表
2‑3）。可以通过将 AdapterView 实例与 Adapter 绑定来填充 AdapterView（如 ListView 或 GridView），此操作会从外部来源检索数据，并创建表示每个数据条目的 View。安卓提供几个 Adapter 子类，用于检索不同种类的数据和构建 AdapterView 的视图，两种最常见的适配器是：

  - ArrayAdapter

在数据源为数组时使用此适配器。默认情况下，ArrayAdapter 会通过对每个数组项调用 toString() 并将内容放入 TextView，为每个项创建视图，例如如果想在 ListView 中显示某个字符串数组，请使用构造函数初始化一个新的 ArrayAdapter，为每个字符串和字符串数组指定布局：

> ArrayAdapter\<String\> adapter = new ArrayAdapter\<String\>(this,
> 
> android.R.layout.simple\_list\_item\_1, myStringArray);

码 2‑48

此构造函数的参数是：

  - Context

  - 包含数组中每个字符串的 TextView 的布局

  - 字符串数组

然后，只需对 ListView 调用 setAdapter()：

> ListView listView = (ListView) findViewById(R.id.listview);
> 
> listView.setAdapter(adapter);

码 2‑49

如需自定义每个项的外观，可以重写数组中各个对象的 toString() 方法，或者如需为 TextView 之外的每个项创建视图（例如如果想为每个数组项创建 ImageView），请扩展 ArrayAdapter 类并替换 getView()，以返回想要为每个项获取的视图类型。

  - SimpleCursorAdapter

在数据来自 Cursor 时使用此适配器。使用 SimpleCursorAdapter 时，必须指定要为 Cursor 中的每个行使用的布局，以及应在哪些布局视图中插入 Cursor 中的哪些列。例如，如果想创建人员姓名和电话号码列表，则可以执行返回 Cursor（包含对应每个人的行，以及对应姓名和号码的列）的查询。然后，您可以创建一个字符串数组，指定您想要在每个结果的布局中包含 Cursor 中的哪些列，并创建一个整型数组，指定应放入每个列的对应视图：

> String\[\] fromColumns = {ContactsContract.Data.DISPLAY\_NAME,
> 
> ContactsContract.CommonDataKinds.Phone.NUMBER};
> 
> int\[\] toViews = {R.id.display\_name, R.id.phone\_number};

码 2‑50

当实例化 SimpleCursorAdapter 时，请传递要用于每个结果的布局、包含结果的 Cursor 以及以下两个数组：

> SimpleCursorAdapter adapter = new SimpleCursorAdapter(this,
> 
> R.layout.person\_name\_and\_number, cursor, fromColumns, toViews, 0);
> 
> ListView listView = getListView();
> 
> listView.setAdapter(adapter);

码 2‑51 SimpleCursorAdapter

然后，SimpleCursorAdapter 会使用提供的布局，将每个 fromColumns 项插入对应的 toViews 视图，从而为 Cursor 中的每个行创建视图。如果在应用的生命周期中更改适配器读取的底层数据，则应调用 notifyDataSetChanged()，这将通知附加的视图数据已被更改，它应该自行进行刷新。可以实现 AdapterView.OnItemClickListener 接口，从而响应 AdapterView 中每一项上的点击事件，例如：

> // Create a message handling object as an anonymous class.
> 
> private OnItemClickListener messageClickedHandler = new
> OnItemClickListener() {
> 
> public void onItemClick(AdapterView parent, View v, int position, long
> id) {
> 
> // Do something in response to the click
> 
> }
> 
> };
> 
> listView.setOnItemClickListener(messageClickedHandler);

码 2‑52 OnItemClickListener

安卓用户界面的布局是可以重用的，重用布局的功能非常强大的，因为其允许创建可重复使用的复杂的布局。在应用程序中，用户界面布局中相同或类似的任何元素都可以被提取出来，定义成一个独立的布局文件单独管理，然后在需要的时候嵌入到另一个布局中，例如一个开关按钮面板或自定义的进度条说明文字等，单独定义后可以嵌入到任何其他布局中，因此可以根据需求灵活地设计自定义的视图，定义自己特殊的布局。如果要有效地重复使用完整的布局，可以在当前布局使用的\<include\>和\<merge\>的标签嵌入到另一个布局。下面使用一个例子来具体说明如何使用\<include\>重用布局。

首先创建一个布局文件title\_bar.xml，其中定义了标题栏和图片，将其作为重用的布局，见码 2‑53。

<table>
<tbody>
<tr class="odd">
<td><blockquote>
<p>&lt;FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"</p>
<p>    <strong>android:</strong>layout_width=”match_parent”</p>
<p>    <strong>android:</strong>layout_height="wrap_content"</p>
<p>    <strong>android:</strong>background="@color/titlebar_bg"&gt;</p>
<p>    &lt;ImageView <strong>android:</strong>layout_width="wrap_content"</p>
<p>               <strong>android:</strong>layout_height="wrap_content"</p>
<p>               <strong>android:</strong>src="@drawable/gafricalogo" /&gt;</p>
<p>&lt;/FrameLayout&gt;</p>
</blockquote></td>
</tr>
</tbody>
</table>

码 2‑53 title\_bar.xml

创建另一个布局文件reuse\_title\_bar.xml，使用\<include\>的标签把title\_bar.xml定义的布局嵌入到这个布局中，见码
2‑30。

<table>
<tbody>
<tr class="odd">
<td><blockquote>
<p>&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"</p>
<p><strong>android:</strong>orientation="vertical"</p>
<p><strong>android:</strong>layout_width=”match_parent”</p>
<p><strong>android:</strong>layout_height=”match_parent”</p>
<p><strong>android:</strong>background="@color/app_bg"</p>
<p><strong>android:</strong>gravity="center_horizontal"&gt;</p>
<p>&lt;include layout="@layout/titlebar"/&gt;</p>
<p>&lt;TextView <strong>android:</strong>layout_width=”match_parent”</p>
<p><strong>android:</strong>layout_height="wrap_content"</p>
<p><strong>android:</strong>text="@string/hello"</p>
<p><strong>android:</strong>padding="10dp" /&gt;</p>
<p>...</p>
<p>&lt;/LinearLayout&gt;</p>
</blockquote></td>
</tr>
</tbody>
</table>

码 2‑54 reuse\_title\_bar.xml

\<merge\>标签在优化UI结构时起到很重要的作用，目的是通过删减多余或者额外的层级，从而优化整个安卓布局的结构。\<merge\>的另外一个用法，就是使用\<merge\>替代布局标签作为重用布局文件的根节点时，当另一个布局文件使用\<Include\>或者ViewStub标签从外部导入其XML结构时，可以很好的将其所包含的子集融合到父级结构中，而不会出现冗余的布局节点。例如，如果在码
2‑29中定义的布局是线性布局，则布局的规则与重用其的码
2‑30相同，使用\<include\>嵌入布局中的组件，与父节点的其他组件都按照同样的排列规则显示，但是从整个安卓布局的结构来看，就多了一个线性布局冗余节点，可以在码
2‑29中使用\<merge\>替代根节点，嵌入其他布局文件后就可以直接采用父节点的布局，与父节点的其他组件在同一级结构中。

<table>
<tbody>
<tr class="odd">
<td><blockquote>
<p>&lt;merge xmlns:android="http://schemas.android.com/apk/res/android"</p>
<p>    <strong>android:</strong>layout_width=”match_parent”</p>
<p>    <strong>android:</strong>layout_height="wrap_content"</p>
<p>    <strong>android:</strong>background="@color/titlebar_bg"&gt;</p>
<p>    &lt;ImageView <strong>android:</strong>layout_width="wrap_content"</p>
<p>               <strong>android:</strong>layout_height="wrap_content"</p>
<p>               <strong>android:</strong>src="@drawable/gafricalogo" /&gt;</p>
<p>&lt;/merge&gt;</p>
</blockquote></td>
</tr>
</tbody>
</table>

码 2‑55 merge\_ title\_bar.xml

下面以目前最常使用的基本布局：约束布局、线性布局、相对布局和表格布局为例，对布局设计和使用进行详细描述。

### 约束布局

约束布局可让使用扁平视图层次结构（无嵌套视图组）创建复杂的大型布局，与相对布局相似，其中所有的视图均根据同级视图与父布局之间的关系进行布局，但其灵活性要高于相对布局，并且更易于与
Android Studio 的布局编辑器配合使用。约束布局的所有功能均可直接通过布局编辑器的可视化工具来使用，因为布局 API
和布局编辑器是专为彼此构建的，因此完全可以使用约束布局通过拖放的形式（而非修改
XML）来构建布局。本节提供了使用约束布局在 Android Studio 3.0
或更高版本中构建布局的指南。

要在约束布局中定义某个视图的位置，必须为该视图添加至少一个水平约束条件和一个垂直约束条件。每个约束条件均表示与其他视图、父布局或隐形引导线之间连接或对齐方式。每个约束条件均定义了视图在竖轴或者横轴上的位置；因此每个视图在每个轴上都必须至少有一个约束条件，但通常情况下会需要更多约束条件。

当将视图拖放到布局编辑器中时，即使没有任何约束条件也会停留在用户放置的位置，不过这只是为了便于修改，当用户在设备上运行布局时，如果视图没有任何约束条件，则会在位置
\[0,0\]（左上角）处进行绘制。在图
2‑36中，布局在编辑器中看起来很完美，但视图C上却没有垂直约束条件。在设备上绘制此布局时，虽然视图C与视图A的左右边缘水平对齐，但由于没有垂直约束条件，其会显示在屏幕顶部。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image65.png" style="width:2.33869in;height:1.12687in" /></p>
<p>图 2‑38 编辑器将视图C显示在视图A下方，但并没有垂直约束条件</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image66.png" style="width:2.35417in;height:1.13433in" /></p>
<p>图 2‑39 视图C现垂直约束在视图A下方</p></td>
</tr>
</tbody>
</table>

尽管缺少约束条件不会导致出现编译错误，但布局编辑器会将缺少约束条件作为错误显示在工具栏中。要查看错误和其他警告，请点击 Show
Warnings and
Errors![](md/media/chapter02_ui_design_basics/media/image67.png)。
为避免出现缺少约束条件这一问题，布局编辑器会使用 [Autoconnect 和 Infer
Constraints](https://developer.android.com/training/constraint-layout?hl=zh-cn#use-autoconnect-and-infer-constraints) 功能自动添加约束条件。如需在项目中使用 约束布局，按以下步骤操作：

(1)确保 maven.google.com 代码库已在模块级 build.gradle 文件中声明；

>    repositories {
> 
> google()
> 
> }    

码 2‑56

(2)将该库作为依赖项添加到同一个 build.gradle 文件中，如以下示例所示。请注意，最新版本可能与示例中显示的不同：

> dependencies {
> 
> implementation "androidx.constraintlayout:constraintlayout:2.1.2"
> 
> // To use constraintlayout in compose
> 
> implementation
> "androidx.constraintlayout:constraintlayout-compose:1.0.0-rc02"
> 
> }

码 2‑57

(3)在工具栏或同步通知中，点击“ Sync Project with Gradle Files”。

现在可以使用 约束布局构建界面。

如需将现有布局转换为约束布局，请按以下步骤操作：

(1)在Android Studio中打开您的布局，然后点击编辑器窗口底部的 Design 标签页；

(2)在Component Tree窗口中右键点击该布局，然后点击“Convert layout to ConstraintLayout”（图
2‑38）。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image68.png" style="width:2.11749in;height:1.66418in" /></p>
<p>图 2‑40用于将布局转换为 约束布局的菜单</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image69.png" style="width:2.87185in;height:1.6194in" /></p>
<p>图 2‑41 可以通过 Attributes 窗口中的 Layout 部分创建连接</p></td>
</tr>
</tbody>
</table>

如需开始新的约束布局文件，请按以下步骤操作：

(1)在 Project 窗口中，点击模块文件夹，然后依次选择“File \> New \> XML \> Layout XML”；

(2)输入该布局文件的名称，并对 Root Tag 输入：

> androidx.constraintlayout.widget.ConstraintLayout

(3)点击 Finish。

如需添加约束条件，执行以下操作：

(1)将视图从 Palette 窗口拖到编辑器中；

当在约束布局中添加视图时，该视图会显示一个边界框，每个角都有用于调整大小的方形手柄，每条边上都有圆形的约束手柄。

(2)点击视图将其选中；

(3)执行以下任一操作：

  - 点击约束手柄并将其拖动到可用定位点。此点可以是另一视图的边缘、布局的边缘或者引导线。请注意，当您拖动约束手柄时，布局编辑器会显示可行的连接定位点和蓝色叠加层。

  - 点击 Attributes 窗口的 Layout 部分中的“Create a
    connection”按钮![](md/media/chapter02_ui_design_basics/media/image70.png)，具体如图
    2‑39所示。

创建约束条件后，编辑器会为其指定默认外边距来分隔两个视图。创建约束条件时，请注意以下规则：

(1)每个视图都必须至少有两个约束条件：一个水平约束条件，一个垂直约束条件。

(2)只能在共用同一平面的约束手柄与定位点之间创建约束条件。因此，视图的垂直平面（左侧和右侧）只能约束在另一个垂直平面上；而基准线则只能约束到其他基准线上。

(3)每个约束句柄只能用于一个约束条件，但您可以在同一定位点上创建多个约束条件（从不同的视图）。

可以通过执行以下任一操作来删除约束条件：

(1)点击某个约束条件将其选中，然后按 Delete。

(2)按住 Control，然后点击某个约束定位点。请注意，该约束条件变为红色即表示可以点击将其删除，如图 2‑40所示。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image71.png" style="width:1.68077in;height:1.85821in" /></p>
<p>图 2‑42红色约束条件表示您可以点击将其删除</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image72.png" style="width:3.02239in;height:1.76564in" /></p>
<p>图 2‑43 点击约束定位点将其删除</p></td>
</tr>
</tbody>
</table>

在 Attributes 窗口的 Layout 部分中，点击某个约束定位点，如图
2‑41所示。如果在视图中添加了相反的约束条件，则约束线会像弹簧一样弯弯曲曲，以此来表示相反的力。当视图尺寸设置为“fixed”或“wrap
content”时，效果最明显，在这种情况下视图在两个约束条件之间居中。如果希望视图扩展其尺寸以满足约束条件，则可以将尺寸切换为“match
constraints”；如果希望保持当前尺寸，但想要移动视图使其不要居中，则可以调整约束偏差。可以使用约束条件来实现不同类型的布局行为，如以下部分所述。

<table>
<thead>
<tr class="header">
<th>约束类型</th>
<th>图示</th>
<th>说明</th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>父级位置</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image73.png" style="width:2.05253in;height:0.99149in" /></td>
<td>将视图的一侧约束到布局的相应边缘。在左图中，视图的左侧连接到父布局的左边缘，可以使用外边距来定义距离边缘的距离。</td>
<td></td>
</tr>
<tr class="even">
<td>顺序位置</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image74.png" style="width:2.0447in;height:0.97775in" /></td>
<td>定义两个视图的显示顺序（垂直或水平方向）。在左图中，B 被约束为始终位于 A 的右侧，而 C 被约束在 A 的下方。 不过，这些约束条件并不意味着对齐，因此 B 仍然可以上下移动。</td>
<td></td>
</tr>
<tr class="odd">
<td>对齐方式</td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image75.png" style="width:1.02978in;height:1.03321in" /></p>
<p>（1）</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image76.png" style="width:1.00746in;height:1.00746in" /></p>
<p>（2）</p></td>
<td>将一个视图的边缘与另一视图的同一边对齐。在左1图 中，B 的左侧与 A 的左侧对齐。 如果要与视图中心对齐，请对两侧创建约束条件。可以通过从约束布局向内拖动视图来偏移对齐量。例如，左2图显示 B 的偏移对齐为 24dp。偏移量由受约束视图的外边距定义。还可以选择要对齐的所有视图，然后点击工具栏中的“Align”图标<img src="md/media/chapter02_ui_design_basics/media/image77.png" style="width:0.13433in;height:0.13433in" />以选择对齐类型。</td>
</tr>
<tr class="even">
<td>基线对齐</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image78.png" style="width:2.19279in;height:1.04856in" /></td>
<td>将一个视图的文本基线与另一视图的文本基线对齐。在左图中，B 的第一行与 A 中的文本对齐。要创建基线约束条件，请右键点击要约束的文本视图，然后点击 “Show Baseline”，接着点击文本基线并将其拖到另一基线上。</td>
<td></td>
</tr>
<tr class="odd">
<td>引导线约束</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image79.png" style="width:2.20358in;height:1.14925in" /></td>
<td>可以添加垂直或水平的引导线来约束视图，并且应用用户看不到该引导线。 您可以根据相对于布局边缘的 dp 单位或百分比在布局中定位引导线。要创建引导线，请点击工具栏中的“Guidelines”<img src="md/media/chapter02_ui_design_basics/media/image80.png" />，然后点击“Add Vertical Guideline”或“Add Horizontal Guideline”，拖动虚线将其重新定位，然后点击引导线边缘的圆圈以切换测量模式。</td>
<td></td>
</tr>
<tr class="even">
<td>屏障约束</td>
<td><img src="md/media/chapter02_ui_design_basics/media/image81.png" style="width:2.1791in;height:2.45486in" /></td>
<td><p>与引导线类似，屏障是一条隐藏的线，您可以用它来约束视图。屏障不会定义自己的位置；相反，屏障的位置会随着其中所含视图的位置而移动。如果您希望将视图限制到一组视图而不是某个特定视图，这就非常有用。</p>
<p>例如，图 13 显示视图 C 被约束在屏障的右侧。该屏障设置为视图 A 和视图 B 的“end”侧（或从左至右布局中的右侧）。因此，屏障根据视图 A 或视图 B 的右侧是否为最右侧来移动。如需创建屏障，请按以下步骤操作：</p>
<p>(1)点击工具栏中的“Guidelines”图标 <img src="md/media/chapter02_ui_design_basics/media/image80.png" style="width:0.10448in;height:0.10448in" />，然后点击“Add Vertical Barrier”或“Add Horizontal Barrier”。</p>
<p>(2)在“Component Tree”窗口中，选择要放入屏障内的视图，然后将其拖动到屏障组件中。</p>
<p>(3)在“Component Tree”中选择障碍，打开 Attributes<img src="md/media/chapter02_ui_design_basics/media/image82.png" style="width:0.14179in;height:0.14179in" /> 窗口，然后设置 barrierDirection。</p>
<p>现在，可以从另一个视图创建屏障约束，还可以将屏障内的视图约束到屏障，这样就可以确保屏障中的所有视图始终相互对齐，即使并不知道哪个视图最长或最高，还可以在屏障内添加引导线，以确保屏障的位置最小。</p></td>
<td></td>
</tr>
</tbody>
</table>

对某个视图的两侧添加约束条件（并且同一维度的视图尺寸为“fixed”或者“wrap
Content”）时，则该视图在两个约束条件之间居中且默认偏差为
50%，可以通过拖动 Attributes 窗口的偏差滑块或拖动视图来调整偏差，如图
2‑42中标记5所示；如果希望视图扩展其尺寸以满足约束条件，可以将尺寸切换为“match
constraints”。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image83.png" style="width:2.09618in;height:2.45522in" /></p>
<p>图 2‑44 在选择视图时，Attributes 窗口会包含如下控件：(1) 尺寸比、(2)删除约束条、(3)高度/宽度模式、(4)外边距和(5)约束偏差，还可以通过点击(6)约束列表中的各个约束条件来突出显示布局编辑器中的各个约束条件</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image84.png" style="width:2.31623in;height:2.49232in" /></p>
<p>图 2‑45 该视图的宽高比设置为 16：9，其宽度基于与高度的比率。</p></td>
</tr>
</tbody>
</table>

可以使用角手柄来调整视图的尺寸，但这会对尺寸进行硬编码，从而使视图不会针对不同的内容或屏幕尺寸进行调整。要选择不同的尺寸模式，请点击视图，然后打开编辑器右侧的
Attributes 窗口。Attributes 窗口顶部附近的视图检查器中包括若干布局属性的控件，如图
2‑42所示（仅适用于约束布局中的视图）。可以通过点击图 2‑42中标注 3
所指示的符号来更改高度和宽度的计算方式。这些符号代表如下所示的尺寸模式（点击符号即可切换这些设置）：

  - Fixed ![](md/media/chapter02_ui_design_basics/media/image85.png) 

可以在下面的文本框中指定具体维度，也可以在编辑器中调整视图尺寸。

  - Wrap Content
    ![](md/media/chapter02_ui_design_basics/media/image86.png) 

视图仅在需要时扩展以适应其内容。

  - Match Constraints
    ![](md/media/chapter02_ui_design_basics/media/image87.png) 

视图会尽可能扩展，以满足每侧的约束条件（在考虑视图的外边距之后），不过可以使用以下属性和值修改该行为（这些属性仅在您将视图宽度设置为“match
constraints”时才会生效）：

  - > layout\_constrasintWidth\_default，spread表示尽可能扩展视图以满足每侧的约束条件，这是默认行为。wrap仅在需要时扩展视图以适应其内容，但如有约束条件限制，视图仍然可以小于其内容，因此与使用
    > Wrap Content（上面）之间的区别在于，将宽度设为 Wrap Content 会强行使宽度始终与内容宽度完全匹配；而使用
    > layout\_constraintWidth\_default 设置为 wrap 的 Match Constraints
    > 时，视图可以小于内容宽度。

  - layout\_constraintWidth\_min 该视图的最小宽度采用 dp 维度。

  - layout\_constraintWidth\_max 该视图的最大宽度采用 dp 维度。

不过，如果给定维度只有一个约束条件，则视图会扩展以适应其内容。在高度或宽度上使用此模式也可让您设置尺寸比。如果至少有一个视图尺寸设置为“match
constraints”(0dp)，可以将视图尺寸设置为 16:9。如需启用该比率，请点击 Toggle Aspect Ratio
Constraint（图 2‑42中的标注 1），然后在出现的输入框中输入 width:height 比率。如果宽度和高度都设置为“match
constraints”，可以点击 “Toggle Aspect Ratio
Constraint”，选择哪个维度基于与另一个维度的比率，视图检查器通过用实线连接相应的边缘来指明哪个被设为比率，例如如果将两侧都设置为“match
constraints”，双击 “Toggle Aspect Ratio
Constraint”，将宽度设置为与高度的比率，现在整个尺寸由视图的高度决定（可以任意方式定义），如图
2‑43所示。要确保所有视图间隔均匀，请点击工具栏中的“Margin”![](md/media/chapter02_ui_design_basics/media/image88.png)，为添加到布局的每个视图选择默认外边距，对默认外边距所做的任何更改仅应用于在更改后添加的视图，可以通过点击代表每个约束条件所在行的数字来控制 Attributes 窗口中每个视图的外边距，例如在图
2‑42中，标注 4 表明下外边距设置为 16dp。

![](md/media/chapter02_ui_design_basics/media/image89.png)

图 2‑46工具栏的 Margin 按钮

工具提供的所有外边距均为 8dp 的倍数，以确保视图与“Material
Design”提供的 8dp方形网格建议保持一致。链是一组视图，这些视图通过双向位置约束条件相互链接到一起，链中的视图可以垂直或水平分布。

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image90.png" style="width:2.24009in;height:1.08209in" /></p>
<p>图 2‑47 具有两个视图的水平链</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image91.png" style="width:2.32836in;height:1.80945in" /></p>
<p>图 2‑48每种链样式的示例</p></td>
</tr>
</tbody>
</table>

链可以采用以下几种样式之一（如图 2‑46）：

  - Spread：视图是均匀分布的（在考虑外边距之后），这是默认值。

  - Spread inside：第一个和最后一个视图固定在链两端的约束边界上，其余视图均匀分布。

  - Weighted：当链设置为 “spread” 或 “spread inside” 时，可以通过将一个或多个视图设置为“match
    constraints”(0dp)来填充剩余空间。默认情况下，设置为“match
    constraints”的每个视图之间的空间均匀分布，但可以使用 layout\_constraintHorizontal\_weight 和 layout\_constraintVertical\_weight 属性为每个视图分配重要性权重。如果熟悉线性布局中的 layout\_weight 的话，就会知道该样式与它的原理是相同的，因此权重值最高的视图获得的空间最大；相同权重的视图获得同样大小的空间。

  - Packed：视图打包在一起（在考虑外边距之后），然后可以通过更改链的头视图偏差调整整条链的偏差（左/右或上/下）。

链的头视图（水平链中最左侧的视图以及垂直链中最顶部的视图）以 XML
格式定义链的样式，不过可以通过选择链中的任意视图，然后点击出现在该视图下方的链按钮 ![](md/media/chapter02_ui_design_basics/media/image92.png)，在“spread”、“spread
inside”和“packed”之间进行切换。如需创建链，选择要包含在链中的所有视图，右键点击其中一个视图，选择 Chains，然后选择 Center
Horizontally 或 Center Vertically，如图 2‑47和图 2‑48中所示：

<table>
<tbody>
<tr class="odd">
<td><p><img src="md/media/chapter02_ui_design_basics/media/image93.png" style="width:2.6339in;height:1.33597in" /></p>
<p>图 2‑49</p></td>
<td><p><img src="md/media/chapter02_ui_design_basics/media/image94.png" style="width:2.94776in;height:1.25373in" /></p>
<p>图 2‑50</p></td>
</tr>
</tbody>
</table>

以下是使用链时需要考虑的其他事项：

  - 视图可以是水平链和垂直链的一部分，因此可以轻松构建灵活的网格布局。

  - 只有当链的每一端都被约束到同一轴上的另一个对象时，链才能正常工作，如图 2‑42所示。

  - 虽然链的方向为垂直或水平，但使用其中一个方向不会沿该方向与视图对齐，因此务必包含其他约束条件，以便使链中的每个视图都能正确定位，例如对齐约束。

可以将每个视图移动到您希望的位置，然后点击“Infer
Constraints”自动创建约束条件，而不是在将视图放入布局中时，为其添加约束条件。“Infer
Constraints”会扫描布局，以便为所有视图确定最有效的约束集，其会尽可能将视图约束在当前位置，同时提高灵活性。可能需要进行一些调整，以确保布局能够按照预期针对不同的屏幕尺寸和方向进行响应。“Autoconnect
to
parent”是可以启用的独立功能。启用后，当将子视图添加到父视图时，此功能会自动为每个视图创建两个或多个约束条件，但仅在可以将视图约束到父布局的情况。“Autoconnect”不会为布局中的其他视图创建约束条件，“Autoconnect”在默认情况下处于停用状态，点击布局编辑器工具栏中的“Enable
Autoconnection to
Parent”![](md/media/chapter02_ui_design_basics/media/image22.png)，即可启用该功能。

### 线形布局

线性布局是基础的，使用得比较多的布局类型之一。线性布局的作用就像其名字一样，根据设置的垂直或水平的属性值，将所有的子控件按垂直或水平进行组织排列。当布局方向设置为垂直时，布局里面的所有子控件被组织在同一列中；当布局方向设置为水平时，所有子控件被组织在一行中，设置线性布局方向的属性为“**android:**orientation”，其值可以为“horizontal”或“vertical”，分别代表水平或垂直方向，见码
2‑34。

> \<LinearLayout
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> android:orientation="horizontal"
> 
> android:layout\_width="fill\_parent"
> 
> android:layout\_height="wrap\_content"\>
> 
> \<\!-- add children here--\>
> 
> \</LinearLayout\>

码 2‑58 LinearLayout语法格式

在这段代码中，还设置了“android:layout\_width”和“android:layout\_height”属性，分别代表了布局的宽度和高度，这两个属性的值可以为“fill\_parent”，其代表将视图扩展以填充所在容器（也就是父容器）的全部空间。我们还可以使用android:gravity属性设置布局内组件的对齐方式，其值可以为top、buttom、left、right、center\_vertical等。设置边距布局的参数有layout\_marginBottom、layout\_marginLeft、layout\_marginRight和layout\_marginTop，分别代表离某元素底边缘、左边缘、右边缘和顶边缘的距离。安卓的Margin和Padding跟HTML的是一样的，见图
2‑49。

<table>
<tbody>
<tr class="odd">
<td><blockquote>
<p><img src="md/media/chapter02_ui_design_basics/media/image95.gif" style="width:2.63433in;height:1.41942in" /></p>
</blockquote>
<p>图 2‑51布局划分的参数定义</p></td>
<td><blockquote>
<p><img src="md/media/chapter02_ui_design_basics/media/image96.png" style="width:1.44776in;height:2.11714in" alt="1" /></p>
</blockquote>
<p>图 2‑52 线性布局实例效果</p></td>
</tr>
</tbody>
</table>

通俗的理解 Padding 为内边框，Margin 为外边框，码 2‑35示例了如何设置一个线性布局的边框。

> **android:**layout\_marginBottom="25dip"
> 
> **android:**layout\_marginLeft="10dip"
> 
> **android:**layout\_marginTop="10dip"
> 
> **android:**layout\_marginRight="10dip"
> 
> **android:**paddingLeft="1dip"
> 
> **android:**paddingTop="1dip"
> 
> **android:**paddingRight="1dip"
> 
> **android:**paddingBottom="1dip"

码 2‑59 设置LinearLayout边框

如果左右上下都是相同的设置则可以按照如下代码直接设置。

> **android:**layout\_marginBottom="25dip"
> 
> **android:**layout\_margin="10dip"
> 
> **android:**padding="5dip"

码 2‑60

LinearLayout所定义的界面上所有的子元素都被堆放在其元素之后，因此一个垂直列表的每一行只会有一个元素，而一个水平列表将会只有一个行高。LinearLayout的可选属性layout\_weight，能够指定每个子控件在父级线性布局中的相对重要程度。LinearLayout还支持为单独的子元素指定权重，这样避免了在一个大屏幕中，一串小对象挤成一堆的情况，而是允许其放大填充空白。子元素指定一个权重值，剩余的空间就会按这些子元素指定的权重比例分配给这些子元素。默认的权重值为0，例如如果有三个文本框，其中两个指定了权重值为1，那么这两个文本框将等比例地放大，并填满剩余的空间，而第三个文本框不会放大，见图
2‑50。要实现这个界面，需要下面几个步骤：

(1)在安卓项目的src目录下，创建显示界面的LinearLayoutActivity类，见码 2‑37；

(2)创建布局文件linear\_layout.xml，存放在/res/layout目录下，见码 2‑38；

(3)修改AndroidManifest.xml文件，在其中添加LinearLayoutActivity的声明，见码 2‑39。

> public class LinearLayoutActivity extends活动{
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> // TODO Auto-generated method stub
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.linear\_layout);
> 
> }
> 
> }

码 2‑61 LinearLayoutActivity.java

码
2‑37中的setContentView(R.layout.linear\_layout)表示把布局文件linear\_layout.xml中定义的控件和排列显示在LinearLayoutActivity定义的活动中。

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<LinearLayout
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**orientation="vertical" \>
> 
> \<LinearLayout
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**orientation="horizontal" \>
> 
> \<TextView
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**background="\#aa0000"
> 
> **android:**gravity="center\_horizontal"
> 
> **android:**text="red" /\>
> 
> \<TextView
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**background="\#00aa00"
> 
> **android:**gravity="center\_horizontal"
> 
> **android:**text="green" /\>
> 
> \<TextView
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**background="\#0000aa"
> 
> **android:**gravity="center\_horizontal"
> 
> **android:**text="blue" /\>
> 
> \<TextView
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**background="\#aaaa00"
> 
> **android:**gravity="center\_horizontal"
> 
> **android:**text="yellow" /\>
> 
> \</LinearLayout\>
> 
> \<LinearLayout
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**layout\_weight="1"
> 
> **android:**orientation="vertical" \>
> 
> \<TextView
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_weight="1"
> 
> **android:**text="row one"
> 
> **android:**textSize="15pt" /\>
> 
> \<TextView
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_weight="1"
> 
> **android:**text="row two"
> 
> **android:**textSize="15pt" /\>
> 
> \<TextView
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_weight="1"
> 
> **android:**text="row three"
> 
> **android:**textSize="15pt" /\>
> 
> \<TextView
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_weight="1"
> 
> **android:**text="row four"
> 
> **android:**textSize="15pt" /\>
> 
> \</LinearLayout\>
> 
> \</LinearLayout\>

码 2‑62 linear\_layout.xml

码
2‑38中定义了三个线性布局。外层的线性布局，通过“android:orientation="vertical"”定义布局内的空间按垂直方向排列。这个外层布局中有两个控件，分别是设置成水平方向和设置成垂直方向的两个线性布局，第一个线性布局中是4个设置成不同颜色的TextView控件，按水平方向排列；第二个线性布局中是4个设置成不同文本的TextView，按垂直方向排列。完成布局文件的定义后，就可以在AndroidManifest.xml文件中添加显示这个界面的
LinearLayoutActivity。

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<manifest xmlns:android="http://schemas.android.com/apk/res/android"
> 
> package="mc.sample"
> 
> **android:**versionCode="1"
> 
> **android:**versionName="1.0" \>
> 
> \<uses-sdk **android:**minSdkVersion="14" /\>
> 
> \<application
> 
> **android:**icon="@drawable/ic\_launcher"
> 
> **android:**label="@string/app\_name" \>
> 
> \<activity
> 
> **android:**name=".LinearLayoutActivity"
> 
> **android:**label="@string/app\_name" \>
> 
> \<intent-filter\>
> 
> \<action **android:**name="android.intent.action.MAIN" /\>
> 
> \<category **android:**name="android.intent.category.LAUNCHER" /\>
> 
> \</intent-filter\>
> 
> \</activity\>
> 
> \</application\>
> 
> \</manifest\>

码 2‑63 LinearLayoutActivity的声明

完成活动的声明之后，在应用程序中就可以运行定义好的LinearLayoutActivity。

### 相对布局

相对布局允许布局中的控件根据其他控件或布局本身的相对位置来指定如何排列，因此可以使用以右对齐，或上下，或置于屏幕中央等形式来排列两个元素。布局中的控件是按顺序排列的，如果第一个元素在屏幕的中央，那么相对于这个元素的其它元素将以屏幕中央的相对位置来排列。如果使用XML布局文件来定义这种布局，之前被关联的元素必须定义。相对布局的相关属性见表
2‑4。

| 属性                                    | 含义                           |
| ------------------------------------- | ---------------------------- |
| **android:**layout\_above             | 将该控件的底部置于给定ID控件之上            |
| **android:**layout\_below             | 将该控件的底部置于给定ID控件之下            |
| **android:**layout\_toLeftOf          | 将该控件的右边缘与给定ID控件左边缘对齐         |
| **android:**layout\_toRightOf         | 将该控件的左边缘与给定ID控件右边缘对齐         |
| **android:**layout\_alignBaseline     | 将该控件的baseline与给定IDbaseline对齐 |
| **android:**layout\_alignTop          | 将该控件的顶部边缘与给定ID顶部边缘对齐         |
| **android:**layout\_alignBottom       | 将该控件的底部边缘与给定ID底部边缘对齐         |
| **android:**layout\_alignLeft         | 将该控件的左边缘与给定ID左边缘对齐           |
| **android:**layout\_alignRight        | 将该控件的右边缘与给定ID右边缘对齐           |
| **android:**layout\_alignParentTop    | 如果为true，将该控件的顶部与其父控件的顶部对齐    |
| **android:**layout\_alignParentBottom | 如果为true，将该控件的底部与其父控件的底部对齐    |
| **android:**layout\_alignParentLeft   | 如果为true，将该控件的左部与其父控件的左部对齐    |
| **android:**layout\_alignParentRight  | 如果为true，将该控件的右部与其父控件的右部对齐    |
| **android:**layout\_centerHorizontal  | 如果为true，将该控件的置于水平居中          |
| **android:**layout\_centerVertical    | 如果为true，将该控件的置于垂直居中          |
| **android:**layout\_centerInParent    | 如果为true，将该控件的置于父控件的中央        |
| **android:**layout\_marginTop         | 上偏移的值                        |
| **android:**layout\_marginBottom      | 下偏移的值                        |
| **android:**layout\_marginLeft        | 左偏移的值                        |
| **android:**layout\_marginRight       | 右偏移的值                        |

表 2‑5 相对布局属性

![1](md/media/chapter02_ui_design_basics/media/image97.png)

图 2‑53相对布局实例效果

图 2‑51是采用RelativeLayout布局显示的效果。要实现这个界面，需要下面几个步骤：

(1)创建显示界面的RelativeLayoutActivity类，见码 2‑40；

(2)创建布局文件relative\_layout.xml，存放在/res/layout目录下，见码 2‑41；

(3)修改AndroidManifest.xml文件，在其中添加RelativeLayoutActivity的声明；

> public class RelativeLayoutActivity extends活动{
> 
> @Override
> 
> protected void onCreate(Bundle savedInstanceState) {
> 
> // TODO Auto-generated method stub
> 
> super.onCreate(savedInstanceState);
> 
> setContentView(R.layout.relative\_layout);
> 
> }
> 
> }

码 2‑64 RelativeLayoutActivity.java

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<RelativeLayout
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent" \>
> 
> \<TextView
> 
> **android:**id="@+id/label"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**text="Type here:" /\>
> 
> \<EditText
> 
> **android:**id="@+id/entry"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_below="@id/label"
> 
> **android:**background="@**android:**drawable/editbox\_background" /\>
> 
> \<Button
> 
> **android:**id="@+id/ok"
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_alignParentRight="true"
> 
> **android:**layout\_below="@id/entry"
> 
> **android:**layout\_marginLeft="10dip"
> 
> **android:**text="OK" /\>
> 
> \<Button
> 
> **android:**layout\_width="wrap\_content"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**layout\_alignTop="@id/ok"
> 
> **android:**layout\_toLeftOf="@id/ok"
> 
> **android:**text="Cancel" /\>
> 
> \</RelativeLayout\>

码 2‑65 relative\_layout.xml

### 表格布局

![Row and Column in Table Layout
Android](md/media/chapter02_ui_design_basics/media/image98.png)

图 2‑54 表格布局

表格布局把用户界面按表格形式划为行和列，然后把控件分配到指定的行或列中。一个表格布局由许多的TableRow组成，每个TableRow定义一行，表格布局容器不会显示行、列或单元格的边框线。每行可有0个或多个的单元格；每个单元格能容纳一个视图对象。列是可拉伸的或可伸缩的，如果是可收缩的，那么列的宽度可以被收缩以适应其父对象的表格；如果是可拉伸的，可以扩大宽度以适应任何额外的可用空间。表格允许单元格为空，但单元格不能跨列，下面是几个主要属性的介绍：

  - android:collapseColumns

折叠列属性用于折叠或隐藏表格布局的列，这些列是表信息的一部分，但不可见。如果值为 0，则第一列显示为折叠状态，即是表格的一部分但是不可见的。

  - android:shrinkColumns

收缩列属性用于缩小或减小列的宽度，可以为此属性指定单列或以逗号分隔的列号列表，指定列中的内容自动换行以减小其宽度。如果值为
0，则第一列的宽度会通过自动换行其内容而缩小或减小；如果值为
0,1，则第一列和第二列都通过自动换行其内容来缩小或缩小；如果值为“\*”，则所有列的内容都被自动换行以缩小它们的宽度。

  - android:stretchColumns

拉伸列属性在表格布局中用于更改列的默认宽度，该宽度设置为等于最宽列的宽度，但我们也可以使用此属性拉伸列以占用可用空间。分配给此属性的值可以是单个列号或以逗号分隔的列号列表，例如1,2,3…n；如果值为
1，则第二列被拉伸以占用行中的任何可用空间，因为列号从 0
开始；如果值为0,1，则表的第一列和第二列都被拉伸以占用行中的可用空间；如果值为“\*”，则所有列都被拉伸以占用可用空间。

![1](md/media/chapter02_ui_design_basics/media/image99.png)

图 2‑55 表格布局实例效果

要实现如图 2‑53界面，需要下面几个步骤：

> (1)创建显示界面的TableLayoutActivity类；
> 
> (2)创建布局文件table\_layout.xml，存放在/res/layout目录下，见码 2‑42
> 
> (3)修改AndroidManifest.xml文件，在其中添加TableLayoutActivity的声明；
> 
> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<TableLayout
> xmlns:android="http://schemas.android.com/apk/res/android"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**stretchColumns="1" \>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**layout\_column="1"
> 
> **android:**padding="3dip"
> 
> **android:**text="Open..." /\>
> 
> \<TextView
> 
> **android:**gravity="right"
> 
> **android:**padding="3dip"
> 
> **android:**text="Ctrl-O" /\>
> 
> \</TableRow\>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**layout\_column="1"
> 
> **android:**padding="3dip"
> 
> **android:**text="Save..." /\>
> 
> \<TextView
> 
> **android:**gravity="right"
> 
> **android:**padding="3dip"
> 
> **android:**text="Ctrl-S" /\>
> 
> \</TableRow\>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**layout\_column="1"
> 
> **android:**padding="3dip"
> 
> **android:**text="Save As..." /\>
> 
> \<TextView
> 
> **android:**gravity="right"
> 
> **android:**padding="3dip"
> 
> **android:**text="Ctrl-Shift-S" /\>
> 
> \</TableRow\>
> 
> \<View
> 
> **android:**layout\_height="2dip"
> 
> **android:**background="\#FF909090" /\>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**padding="3dip"
> 
> **android:**text="X" /\>
> 
> \<TextView
> 
> **android:**padding="3dip"
> 
> **android:**text="Import..." /\>
> 
> \</TableRow\>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**padding="3dip"
> 
> **android:**text="X" /\>
> 
> \<TextView
> 
> **android:**padding="3dip"
> 
> **android:**text="Export..." /\>
> 
> \<TextView
> 
> **android:**gravity="right"
> 
> **android:**padding="3dip"
> 
> **android:**text="Ctrl-E" /\>
> 
> \</TableRow\>
> 
> \<View
> 
> **android:**layout\_height="2dip"
> 
> **android:**background="\#FF909090" /\>
> 
> \<TableRow\>
> 
> \<TextView
> 
> **android:**layout\_column="1"
> 
> **android:**padding="3dip"
> 
> **android:**text="Quit" /\>
> 
> \</TableRow\>
> 
> \</TableLayout\>

码 2‑66 table\_layout.xml

## 理解样式

样式是用于指定视图或窗口的外观和格式的一系列属性集合，可以指定控件或布局的高度、内边框、字体颜色、字体大小、背景颜色等等属性。安卓中的样式与网页设计中的层叠样式表有着相似的原理，就是允许设计从内容中分离出来， 例如使用一个样式，可以将码
2‑43简化为码 2‑44：

> \<TextView
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**textColor="\#00FF00"
> 
> **android:**typeface="monospace"
> 
> **android:**text="@string/hello" /\>

 码 2‑67

简化成这样：

> \<TextView
> 
> style="@style/CodeFont"
> 
> **android:**text="@string/hello" /\>

码 2‑68

将所有与样式相关的属性从XML布局中移出，放到一个名为CodeFont 的样式定义中，通过样式属性应用。主题是一个应用于整个活动或应用中，而不是某一个单独的视图。当一个样式被作为主题来应用时，则这个样式对活动或应用中的每个视图都有效，例如把CodeFont样式作为主题应用于一个活动，那么这个活动中所有文本都将是绿色等宽字体。

### 定义样式

如果要创建一套样式，需要在项目的res/values/ 目录下创建一个XML文件，来定义样式。定义样式的XML文件名称任意指定，但必须使用.xml作为后缀，保存在res/values/ 文件夹中，而且文件中的根节点必须是\<resources\> 。\<resources\>节点下由style子元素定义样式的具体配置，其name属性所创建样式的唯一标识。style元素下可以有多个\<item\>子元素，具体来定义视图各属性的配置。\<item\>元素包含一个name属性和一个对应值，说明这一项设定哪个视图的属性的样式。\<item\> 元素本身的值可以是一个关键字符串、十六进制颜色、或另一个资源类型的引用或其他值，其是属性的样式值。码
2‑45是CodeFont的样式定义，可以看出样式定义XML文件的结构和语法。

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<resources\>
> 
> \<style name="CodeFont"
> parent="@**android:**style/TextAppearance.Medium"\>
> 
> \<item name="**android:**layout\_width"\>fill\_parent\</item\>
> 
> \<item name="**android:**layout\_height"\>wrap\_content\</item\>
> 
> \<item name="**android:**textColor"\>\#00FF00\</item\>
> 
> \<item name="**android:**typeface"\>monospace\</item\>
> 
> \</style\>
> 
> \</resources\>

码 2‑69 **style\_sample.xml**

每个\<resources\> 元素的子节点在编译时都被转换为一个应用程序资源对象，可通过\<style\> 元素的name 属性的值来引用，例如前面代码中样式，通过style="@style/CodeFont"语句来引用。在\<style\> 元素中的parent 属性是可选的，能够从指定的样式中继承所有属性。通过这种途径从一个现有的样式中继承属性后，可以根据需求改变或添加的属性，从而创建新的样式，例如下面的样式定义是从安卓平台默认文本外观样式继承，修改了一下文本的颜色。

> \<style name="GreenText" parent="@**android:**style/TextAppearance"\>
> 
> \<item name="**android:**textColor"\>\#00FF00\</item\>
> 
> \</style\>

码 2‑70

 如果要继承的是自定义的样式，就不必使用parent 属性，而使用“.”把原有的样式和新样式名连接起来，例如下面的代码创建了一个新样式，其继承前面定义的CodeFont，但把颜色改为红色。

> \<style name="CodeFont.Red"\>
> 
> \<item name="**android:**textColor"\>\#FF0000\</item\>
> 
> \</style\>

码 2‑71

这里没有使用parent 属性，name 属性以CodeFont 起始，使用“.”连接了后面的新样式名称，这个新样式可以通过@style/CodeFont.Red 来引用。样式的继承可以有多重，例如下面的代码，从CodeFont 和CodeFont.Red 样式中同时继承，然后添加**android:**textSize 属性。

> \<style name="CodeFont.Red.Big"\>
> 
> \<item name="**android:**textSize"\>30sp\</item\>
> 
> \</style\>

码 2‑72

这种技巧仅适用于将自定义的资源链接起来，不能用这种方式继承安卓内置的样式，要引用一个安卓的内置样式，必须使用“parent ”属性。

### 使用样式

定义一个样式之后，如果对一个视图应用了这个样式，而这个视图并不支持此样式中设定的某些属性，那么此视图将应用那些其支持的属性，并简单忽略那些不支持的。在活动或应用程序中有两种方式来使用样式：

(1)对一个独立的视图，在布局文件XML中将样式属性添加到的此视图元素中；

(2)对一个活动或应用，在AndroidManifest.xml文件中将android:theme 属性添加到的\<activity\> 或\<application\> 元素中。

如果将一个样式应用到布局中一个单独的视图上时，此样式定义的属性会仅应用于那个视图；如果一个样式应用到一个视图组上，其子视图元素并不会继承应用此样式属性，只有直接设置其子元素此样式才会起作用，但是通过第二种方式，将样式作为主题来应用的方式，将会把这个样式应用到此活动或\<application\> 的所有视图元素上。下面是在XML布局中为视图设置样式的简单语法：

> \<TextView
> 
> style="@style/CodeFont"
> 
> **android:**text="@string/hello" /\>

码 2‑73

如果需要对应用程序中所有活动设置一个主题，则打开AndroidManifest.xml 文件并编辑\<application\> 标签，使之包含**android:**theme 属性和样式名称，具体设置代码如下。

> \<application **android:**theme="@style/CustomTheme"\>

码 2‑74

如果希望主题仅应用到应用程序中的某个活动中，那么就将**android:**theme 属性添加到\<activity\> 标签里。

##  理解资源

安卓应用程序不仅包括逻辑代码还包括资源文件，例如字符、图片、布局和语言支持等。安卓系统对于资源的管理使用了一种将资源外部化的模式，使得应用程序可以在代码编译时，只是使用资源的引用，在代码编译后修改资源包含的内容也不会影响程序的逻辑，从而保持程序逻辑和资源的各自独立。对于外部资源，可以通过提供替代资源的方式，支持不同的语言或屏幕大小。随着越来越多不同的配置的安卓设备的出现，这种模式，对于安卓程序运行于复杂多变的环境尤其重要。

安卓的资源以文件形式，在项目的res/目录下进行统一管理。为了提供具有不同配置的兼容性，必须在项目的res/目录中组织资源，在其不同子目录中存放不同的资源类型和配置。对于任何类型的资源，都可以指定默认情况下使用的资源和多个替代资源。

使用默认资源的条件是指可以支持任何配置的安卓设备或当前的配置没有替代资源匹配，**图
2‑54**中的界面只设计有一种布局应用到两种设备，界面显示不能适应变化；而替代的资源是为特定设备配置设计的，**图
2‑55**中的界面为横向的屏幕设置了替代布局，对于大屏幕的设备可以合理的规划界面显示。通过资源文件目录名，安卓系统会自动应用相应的资源文件，匹配设备当前的配置。

![](md/media/chapter02_ui_design_basics/media/image100.png)

图 2‑56 布局设计之一

![](md/media/chapter02_ui_design_basics/media/image101.png)

图 2‑57 布局设计之二

对于res/目录下的资源，应用程序可以通过引用资源ID号来调用，具体的资源ID号能够从R.java中查到。对于每一种资源类型都有一个R的子类对应着，例如R.drawable中包含着所有drawable资源，并且对每个特定类型的所有资源都有一个静态的整型数值一一对应，如R.drawable.icon。这个整型数值就是这个特定资源的ID号，通过其能获取的对应资源。一个资源的ID号一般的组成如下：

  - 资源类型

每种资源都会被分组到一种特定的资源类型，例如string， drawable，和 layout 等，还有更多的资源类型，如raw，color等。

  - 资源名

同时也是文件名，不包括拓展名；或者是XML 中**android:**name属性的值，条件是这个资源是一个简单的值，如一个字符串。

### 提供资源

res/的子目录中包含了所有资源，资源目录名是很重要的，表 2‑5列出了资源目录和具体资源类型的对照。

| 目录        | 资源类型                                               |
| --------- | -------------------------------------------------- |
| animator/ | 存放定义属性动画的XML文件                                     |
| anim/     | 存放定义补建动画的XML文件                                     |
| color/    | 存放定义颜色值的XML文件                                      |
| drawable/ | 存放位图文件（.png、.jpg、.gif、.9.png），或者是被编译成可描画资源类型的XML文件 |
| layout/   | 存放定义用户界面布局的XML文件                                   |
| menu/     | 存放定义应用程序菜单的XML文件，如选项菜单、上下文菜单、或子菜单                  |
| raw/      | 存放任意原生格式的文件                                        |
| values/   | 存放包含简单值的XML文件，如字符串、整数以及颜色等                         |
| xml/      | 放在这个目录下的任意XML文件，都可在运行时通过调用Resources.getXML()方法来读取  |

表 2‑6 资源目录名

值得注意的是不能把资源文件直接保存在res/目录中，这样会导致编译错误。保存在表
2‑5中所定义子目录中的资源是默认资源，就是说这些资源定义了安卓应用程序用户界面的默认设计和内容。但是对于同一个应用程序来说，可以根据安卓设备的不同特性和设置预先定义不同类型的资源，以方便应用程序的用户界面切合运行时的硬件设备。例如，可以针对竖屏和横屏设定不同的布局资源文件，以满足屏幕切换的需要；或者也可以针对不同的语言，提供不同的字符串资源，使得在用户界面上显示与设备语言相匹配的文字。要给不同的设备配置提供这些不同的资源，除了默认的资源以外，还要提供可选的替代资源。

### 访问资源

安卓应用程序引用某个资源时，有两种方法。

(1)在Java应用程序代码中直接调用，通过调用[Resources](http://developer.android.com/reference/android/content/res/Resources.html)类中的方法来获取某一特定的资源，通过getResources()方法得到Resources类的一个实例，在应用程序代码中引用资源的语法如下：

> \[\<package\_name\>.\]R.\<resource\_type\>.\<resource\_name\>

码 2‑75

其中
\<package\_name\>指资源所在的包名，如果资源文件在项目本身的包内时，该字段不需要填写。\<resource\_type\>指R类下对应一种特定资源类型的子类，如R.String。
\<resource\_name\>可以是不包含文件扩展名的资源文件名或者XML元素中**android:**name
属性的值。例如，Java应用程序中的语句“R.drawable.my\_background\_image”调用了资源类型为drawable，资源名为my\_background\_image的安卓资源，实际上就是引用了安卓定义好的图片资源。

> //使用drawable类型的图片资源给当前屏幕加载背景
> 
> getWindow().setBackgroundDrawableResource
> 
> (R.drawable.my\_background\_image) ;
> 
> //使用Layout类型的布局资源作为当前屏幕的布局
> 
> setContentView(R.layout.main\_screen);

码 2‑76

(2)在XML中调用，通过特殊的XML语法引用R.class文件中的相关资源ID，在XML资源文件中引用资源的语法如下：

> @\[\<package\_name\>:\]\<resource\_type\>/\<resource\_name\>

码 2‑77

语句中各标记的含义同上。例如，XML布局文件中的语句“@color/opaque\_red”和“@string/hello”调用了资源类型分别为Color和String，资源名分别为opaque\_red和hello的安卓资源。

> //使用Color资源类型的opaque\_red的颜色资源作为文本颜色
> 
> **android:**textColor="@color/opaque\_red"
> 
> //使用String资源类型的hello的字符串作为文本显示的内容
> 
> **android:**text="@string/hello" /\>

码 2‑78

上面的语句中的资源定义都在本项目中，这种情况不需要说明包，如果要引用安卓系统定义的资源则需要包含包名，例如：

> \<?xml version="1.0" encoding="utf-8"?\>
> 
> \<EditText xmlns:android="http://schemas.android.com/apk/res/android"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="fill\_parent"
> 
> **android:**textColor="@**android:**color/secondary\_text\_dark"
> 
> **android:**text="@string/hello"/\>

码 2‑79

应该在任何时候都使用字符串资源，以便应用程序能够针对其他语言进行本地化。可以在任何需要使用自己提供的资源的地方，通过这两种语法来调用。在安卓系统中，不仅仅资源本身可以被引用，在定义样式时也可以引用样式属性。引用样式属性的语法与普通的资源格式几乎是等同的，但是使用问号“?”取代符号“@”，资源类型部分是可选的，语法格式如下。

> ?\[\<package\_name\>:\]\[\<resource\_type\>/\]\<resource\_name\>

码 2‑80

下面的例子是引用安卓设定的一个样式属性textColorSecondary的来设置布局中TextView的文本颜色，使得其匹配系统主题的主文本的颜色，这里不需要说明样式属性的资源类型。

> \<EditText id="text"
> 
> **android:**layout\_width="fill\_parent"
> 
> **android:**layout\_height="wrap\_content"
> 
> **android:**textColor="?**android:**textColorSecondary"
> 
> **android:**text="@string/hello\_world" /\>

码 2‑81

安卓中包含有很多标准的资源，例如样式、主题、布局等等，要调用这些资源需要通过安卓包名来限定这些资源。

## 多屏幕适应

移动终端的类型繁多，屏幕的尺寸、长宽大小比例也多种多样。安卓应用程序要运行在不确定的屏幕上，安卓系统需要处理适配不同显示屏幕的工作。因此安卓系统对不同的屏幕尺寸和密度提供API，应用程序能够设计提供不同的屏幕尺寸和密度的用户界面。应用程序运行时，系统通过适当的管理方式，针对当前的屏幕配置引用与对应的资源进行适配，调整布局和位图。如果没有替代的布局资源，系统则采用屏幕默认设计，并根据屏幕的大小进行缩放和调整。但通过提供多种屏幕布局的方式，可以最大程度优化用户体验。安卓系统是怎样支持多种屏幕的呢？在应用程序用户界面设计中，可以从以下几个方面来设定对多种屏幕的支持。

  - 明确声明应用程序支持的所有屏幕尺寸

在AndroidManifest.xml文件中，使用\<supports-screens\>元素说明应用程序能够支持的屏幕尺寸。通过声明应用程序支持的屏幕尺寸，可以保证只有那些屏幕尺寸被应用程序支持的设备才可以下载该应用程序。

  - 为不同的屏幕尺寸提供不同的布局

默认情况下，安卓可以重新调整应用程序的布局，以适应当前的设备屏幕。但有时候可能调整后的屏幕布局不太美观，这就需要应用程序针对特殊的屏幕（过大、或过小）设计不同的布局。在大多数情况下，这是可行的。使用配置限定符能够提供尺寸相关的资源，这些限定符包括small、normal、large和xlarge。如果应用程序运行在安卓
3.2（API Level
13）以上，则需要使用“sw\<N\>dp”配置限定符定义布局资源所需的最小可用宽度，例如600dp屏幕以上的布局，放置在layout-sw600dp/目录下，下面代码就是一个设定布局的简单例子。

> res/layout/my\_layout.xml // layout for normal screen size ("default")
> 
> res/layout-small/my\_layout.xml // layout for small screen size
> 
> res/layout-large/my\_layout.xml // layout for large screen size
> 
> res/layout-xlarge/my\_layout.xml // layout for extra large screen size
> 
> res/layout-xlarge-land/my\_layout.xml // layout for extra large in
> landscape orientation

码 2‑82

  - 为不同的屏幕尺寸提供不同的位图

默认情况下，安卓系统会在应用程序运行时，根据运行设备的屏幕缩放位图文件（.png、.jpg和.gif）和Nine-Patch文件（.9.png），使其显示出合适的物理尺寸。但这种缩放有时会失真，最好的方法是为不同的屏幕密度提供不同分辨率的位图。安卓系统使用限定符ldpi(low)、mdpi(medium)、hdpi(high)和xhdpi(extra
high)，来提供密度相关的资源。见如下代码，为高密度屏幕提供的位图放在drawable-hdpi/目录下。

> res/drawable-mdpi/my\_icon.png // bitmap for medium density
> 
> res/drawable-hdpi/my\_icon.png // bitmap for high density
> 
> res/drawable-xhdpi/my\_icon.png // bitmap for extra high density

码 2‑83

当安卓应用程序运行时，系统怎样使用适当的可选资源呢？系统会根据当前屏幕的大小和密度配置，查找应用程序提供的布局和密度相关资源，确定最佳匹配的资源目录下的资源，例如应用程序在一个高密度的大屏幕上显示一个drawable资源，系统会寻找最接近此大屏幕的布局，高密度drawable目录的资源来使用。如果没有匹配的资源是可用的，系统将使用默认的资源并且对其进行缩放来适应当前的屏幕尺寸和密度。默认的资源是那些没有配置限定符的资源。

## 小结

本章主要介绍了活动和片段的概念和生命周期，以及如何使用活动和片段类创建用户界面。活动是安卓的四大基本组件之一，通过活动用户可以与移动终端进行交互。活动的生命周期中有Active/Running、Paused、Stopped和Killed四种状态。本章还着重介绍了布局概念和分类。活动中的具体图形控件由安卓定义的View类和ViewGroup类的子类对象组成，这些对象在活动中的排列结构，称为用户界面的布局。本章介绍了四种基本的布局对象：约束布局、线性布局、相对布局和表格布局。安卓的用户界面布局是在XML文件中静态记载，也可以在安卓的Java程序中动态加载的，针对每一种基础布局，使用具体的代码实现说明了如何在用户界面中使用这些布局，还介绍了如何使用安卓项目中的样式和资源的概念。
