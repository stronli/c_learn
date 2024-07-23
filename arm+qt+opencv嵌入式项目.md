# opencv嵌入式项目

## 1、数字图像处理(c++ opencv)

### 1.1、图像的读取、显示和保存

```
#include<opencv2/opencv.hpp>
#include<iostream>


using namespace cv;
using namespace std;

int main()
{
	Mat image;   //创建一个空图像image
	image = imread("D://work//c++//Imageprocessing1//企鹅.jpg");  //读取文件夹中的图像

	//检测图像是否加载成功
	if (image.empty())  //检测image有无数据，无数据 image.empty()返回 真
	{
		cout << "Could not open or find the image" << endl;
		return -1;
	}

	namedWindow("IMAGE");  //创建显示窗口，不加这行代码，也能显示，默认窗口大小不能改变
	imshow("IMAGE", image);  //在窗口显示图像

	imwrite("1.png", image); //保存图像为png格式，文件名称为1

	waitKey(0);  //暂停，保持图像显示，等待按键结束

	return 0;

}
```

### 1.2、对应函数

1.2.1、imread函数

```
cv::Mat cv::imread(const string& filename, int flags = cv::IMREAD_COLOR)
```

其中：

- `filename` 是要读取的图像文件的路径。
- `flags` 是可选参数，用于指定图像加载的方式。默认情况下，它是 `cv::IMREAD_COLOR`，表示以彩色图像的方式加载。您也可以使用其他选项，例如 `cv::IMREAD_GRAYSCALE`（以灰度图像方式加载）、`cv::IMREAD_UNCHANGED`（以原始图像的方式加载，包括 alpha 通道等）。

1.2.2、namedWindow函数

```
void cv::namedWindow(const string& winname, int flags = cv::WINDOW_AUTOSIZE)
其中：

winname 是要创建的窗口的名称。
flags 是可选参数，用于指定窗口的属性。默认情况下，它是 cv::WINDOW_AUTOSIZE，表示窗口大小会根据图像的实际大小自动调整。您也可以选择 cv::WINDOW_NORMAL，表示窗口大小可调整。
```

1.2.3、imshow函数

```
void cv::imshow(const string& winname, cv::InputArray mat)
其中：

winname 是要显示图像的窗口的名称。窗口必须先使用 namedWindow 函数创建。
mat 是要显示的图像数据，通常是一个 cv::Mat 对象。
```

1.2.4 imwrite函数

```
bool cv::imwrite(const string& filename, InputArray img, const vector<int>& params = vector<int>())
其中：

filename 是要保存图像的文件路径。
img 是要保存的图像数据，通常是一个 cv::Mat 对象。
params 是可选参数，用于指定保存图像的格式和压缩参数。它是一个整数向量，包含特定于图像格式的参数。
```

### 1.3、创建图像（矩阵）：Mat

使用Mat创建图像（矩阵）的常用形式有：
1.创建一个空图像，大小为0

```text
 Mat image1;
```



2.指定矩阵大小，指定数据类型：

```text
Mat image1(100,100,CV_8U);
```

这里包含三个参数：矩阵行数，矩阵列数，数据类型；

其中数据类型有很多种，常用的应该有：

CV_8U：8位无符号型（0~255），即灰度图像；

CV_8UC3：三通道8位无符号型，这里三通道指B（蓝）G（绿）R（红），与matlab中的RGB正好相反。

这里创建矩阵时未指定矩阵的值，发现默认值的大小为205.



3.指定矩阵大小，指定数据类型，设置初始值：

```text
Mat image1(100,100,CV_8U, 100);
```

这里包含四个参数：矩阵行数，矩阵列数，数据类型，初始值；
对于灰度图像：可以直接给出初始值，也可以使用Scalar（）；

```text
 Mat image1(100,100,CV_8U, 100);
Mat image1(100,100,CV_8U, Scalar(100));
```

对于三通道图像：使用Scalar（）；

```text
Mat image1(100,100,CV_8UC3, Scalar(100,100,100));
```

### 1.4、获取图像信息

获取图像的宽度（列数），高度（行数），尺寸和通道数：

```text
#include<iostream>
#include<opencv2/opencv.hpp>

using namespace cv;
using namespace std;

int main()
{
	Mat image1 = imread("lena.png");  //读取图像；
	if (image1.empty())
	{
		cout << "读取错误" << endl;
		return -1;
	}

	imshow("image1", image1);  //显示图像；

	cout << "图像的行数为： " << image1.rows << endl;  //获取图像的高度，行数；
	cout << "图像的列数为： " << image1.cols << endl;  //获取图像的宽度，列数；
	cout << "图像的通道数为： " << image1.channels() << endl;  //获取图像的通道数，彩色图=3，灰度图=1；
	cout << "图像的尺寸为： " << image1.size << endl;  //获取图像的尺寸，行*列；

	waitKey(0);  //暂停，保持图像显示，等待按键结束

	return 0;
}
```

## 2、数字图像处理( c++ opencv)：通过鼠标点击操作获取图像的像素坐标和像素值

### 1、创建鼠标操作函数的头文件：onMouse.h

```
#pragma once
#include<iostream>
#include<opencv2/opencv.hpp>

using namespace cv;
using namespace std;

void onMouse(int event, int x, int y, int flags, void* param);  //evnet:鼠标事件类型 x,y:鼠标坐标 flags：鼠标哪个键

void onMouse(int event, int x, int y, int flags, void* param)  //evnet:鼠标事件类型 x,y:鼠标坐标 flags：鼠标哪个键
{
	Mat* im = reinterpret_cast<Mat*>(param);
	switch (event) {

	case EVENT_LBUTTONDOWN:
		//显示图像像素值

		if (static_cast<int>(im->channels()) == 1)
		{
			//若图像为单通道图像，则显示鼠标点击的坐标以及灰度值
			switch (im->type())
			{
			case 0:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<uchar>(Point(x, y))) << endl; break;
			case 1:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<char>(Point(x, y))) << endl; break;
			case 2:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<ushort>(Point(x, y))) << endl; break;
			case 3:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<short>(Point(x, y))) << endl; break;
			case 4:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<int>(Point(x, y))) << endl; break;
			case 5:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<float>(Point(x, y))) << endl; break;
			case 6:
				cout << "at (" << x << ", " << y << " ) value is: " << static_cast<int>(im->at<double>(Point(x, y))) << endl; break;
			}
		}
		else
		{
			//若图像为彩色图像，则显示鼠标点击坐标以及对应的B, G, R值
			cout << "at (" << x << ", " << y << ")"
				<< "  B value is: " << static_cast<int>(im->at<Vec3b>(Point(x, y))[0])
				<< "  G value is: " << static_cast<int>(im->at<Vec3b>(Point(x, y))[1])
				<< "  R value is: " << static_cast<int>(im->at<Vec3b>(Point(x, y))[2])
				<< endl;
		}

		break;
	}
}

//setMouseCallback("image1", onMouse, reinterpret_cast<void*>(&image1)); //关联图像显示窗口和onMouse函数


//Mat每个格子内的数据格式---------- - Mat定义
//Mat_<uchar>-------- - CV_8U（0-255）
//Mat<char>---------- - CV_8S（-128-127）
//Nat_<short>-------- - CV_16S（-32768-32767）
//Mat_<ushort>--------CV_16U（0-65535）
//Mat_<int>---------- - CV_32S（-2147483648-2147483647）
//Mat_<float>----------CV_32F（-FLT_MAX…FLT_MAX，INF，NAN）
//Mat_<double>--------CV_64F（-DBL_MAX…DBL_MAX，INF，NAN）

//Mat数据类型和通道对应的type()
//              C1          C2          C3          C4
//CV_8U         0           8           16          24
//CV_8S         1           9           17          25
//CV_16U        2          10           18          26
//CV_16S        3          11           19          27
//CV_32S        4          12           20          28
//CV_32F        5          13           21          29
//CV_64F        6          14           22          30
```

```
#include<iostream>
#include<opencv2/opencv.hpp>
#include "onMouse.h"

using namespace cv;
using namespace std;

int main()
{ 

	Mat image1 = imread("lena.png");  //读取图像；
	if (image1.empty())
	{
		cout << "读取错误" << endl;
		return -1;
	}
	imshow("image1",image1);  //显示图像；
	
	
	setMouseCallback("image1", onMouse, reinterpret_cast<void*>(&image1)); //关联图像显示窗口和onMouse函数
	waitKey(0);  //暂停，保持图像显示，等待按键结束

	return 0;
}
```

### 2、考勤表设置

![image-20240422151719311](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240422151719311.png)

创建两个表一个是员工信息表，一个是考勤表

### 3、使用qt自带的sqlite3

#### 前言

SQLite（sql）是一款开源轻量级的数据库软件，不需要server，可以集成在其他软件中，非常适合嵌入式系统。
Qt5以上版本可以直接使用SQLite（Qt自带驱动）。

#### 用法

#### 1 准备

引入SQL模块
在Qt项目文件(.pro文件)中，加入SQL模块：

```
QT += sql
引用头文件
在需要使用SQL的类定义中，引用相关头文件。例如：
```

```
#include <QSqlDatabase>
#include <QSqlError>
#include <QSqlQuery>
```

#### 2 使用

##### 1. 建立数据库

检查连接、添加数据库驱动、设置数据库名称、数据库登录用户名、密码。

```
QSqlDatabase database;
if (QSqlDatabase::contains("qt_sql_default_connection"))
{
    database = QSqlDatabase::database("qt_sql_default_connection");
}
else
{
    database = QSqlDatabase::addDatabase("QSQLITE");
    database.setDatabaseName("MyDataBase.db");
    database.setUserName("XingYeZhiXia");
    database.setPassword("123456");
}



```

上述代码解释：
（1）第一行中，建立了一个QSqlDatabase对象，后续的操作要使用这个对象。
（2）if语句用来检查指定的连接(connection)是否存在。这里指定的连接名称（connection name）是qt_sql_default_connection，这是Qt默认连接名称。实际使用时，这个名称可以任意取。如果判断此连接已经存在，那么QSqlDatabase::contains()函数返回true。此时，进入第一个分支，QSqlDatabase::database()返回这个连接。
（3）如果这个连接不存在，则进入else分支，需要创建连接，并添加数据库。在else分支第一行，addDatabase()的参数QSQLITE是SQLite对应的驱动名，不能改。而且需要注意的是，addDatabase()的第二个参数被省略了，第二个参数的默认参数就是上面提到的Qt默认连接名称qt_sql_default_connection。如果需要使用自定义的连接名称（如果程序需要处理多个数据库文件的话就会这样），则应该加入第二个参数，例如

```
database = QSqlDatabase::addDatabase("QSQLITE", "my_sql_connection);
```

这个时候，如果在另一个地方需要判断my_sql_connection连接是否存在，就应该使用if (QSqlDatabase::contains(“my_sql_connection”))。
（4）else分支第二行中，setDatabaseName()的参数是数据库文件名。如果这个数据库不存在，则会在后续操作时自动创建；如果已经存在，则后续的操作会在已有的数据库上进行。
（5）else分支后面两行，设置用户名和密码。用户名，密码都可以随便取，也可以省略。

##### 2. 打开数据库

使用open()打开数据库，并判断是否成功。注意，在第一步检查连接是否存在时，如果连接存在，则在返回这个连接的时候，会默认将数据库打开。

```
if (!database.open())
{
    qDebug() << "Error: Failed to connect database." << database.lastError();
}
else
{
    // do something
}
```

如果打开成功，则进入else分支。对数据库的操作都需要在else分支中进行。

##### 3.关闭数据库

数据库操作完成后，最好关闭。

```
 database.close();
```

##### 4.操作数据库

对数据库进行操作需要用到QSqlQuery类，操作前必须定义一个对象。下面举例说明操作方法。操作需要使用SQLite语句，本文中的几个例子会使用几个常用的语句，关于SQLite语句的具体信息请参考SQLite相关资料。

###### 例1：创建表格

创建一个名为student的表格，表格包含三列，第一列是id，第二列是名字，第三列是年龄。

```
QSqlQuery sql_query;
QString create_sql = "create table student (id int primary key, name varchar(30), age int)";
sql_query.prepare(create_sql);
if(!sql_query.exec())
{
    qDebug() << "Error: Fail to create table." << sql_query.lastError();
}
else
{
    qDebug() << "Table created!";
}
```

代码解释：
（1）第一行定义一个QSqlQuery对象。
（2）第二行是一个QString，其中的内容是SQLite语句。对数据库的操作，都是用SQLite的语句完成的，把这些指令以QString类型，通过prepare函数，保存在QSqlQuery对象中。也可将指令，以QString形式直接写在exec()函数的参数中，例如：

```
    sql_query.exec("create table student (id int primary key, name varchar(30), age int)");
```

创建表格语句：create table <table_name> (f1 type1, f2 type2,…);
create table是创建表格的语句，也可用大写CREATE TABLE；student是表格的名称，可以任意取；括号中是表格的格式，上述指令表明，表格中有三列，第一列的名称（表头）是id，这一列储存的数据类型是int，第二列名称是name，数据类型是字符数组，最多有30个字符（和char(30)的区别在于，varchar的实际长度是变化的，而char的长度始终是给定的值），第三列的名称是age，数据类型是int。
如果sql_query.exec()执行成功，则创建表格成功。

###### 例2：插入数据

在刚才创建的表格中，插入一行数据。

```
QString insert_sql = "insert into student values (?, ?, ?)";
sql_query.prepare(insert_sql);
sql_query.addBindValue(max_id+1);
sql_query.addBindValue("Wang");
sql_query.addBindValue(25);
if(!sql_query.exec())
{
    qDebug() << sql_query.lastError();
}
else
{
    qDebug() << "inserted Wang!";
}
if(!sql_query.exec("INSERT INTO student VALUES(3, \"Li\", 23)"))
{
    qDebug() << sql_query.lastError();
}
else
{
    qDebug() << "inserted Li!";
}
```


插入语句：insert into <table_name> values (value1, value2,…);
insert into是插入语句，student是表格名称，values()是要插入的数据。这里，我们插入了2组数据。插入第一组数据的时候，用addBindValue来替代语句中的?，替代的顺序与addBindValue调用的顺序相同。插入第二组数据的时候，则是直接写出完整语句。

###### 例3：更新数据（修改数据）

```
QString update_sql = "update student set name = :name where id = :id";
sql_query.prepare(update_sql);
sql_query.bindValue(":name", "Qt");
sql_query.bindValue(":id", 1);
if(!sql_query.exec())
{
    qDebug() << sql_query.lastError();
}
else
{
    qDebug() << "updated!";
}
语句：update <table_name> set <f1=value1>, <f2=value2>… where ;
更新（修改）的语句是update…set…，其中student是表格名称，name是表头名称（即第二列），:name是待定的变量，where用于确定是哪一组数据，:id也是待定变量。
bindValue(" ", " ")函数用来把语句中的待定变量换成确定值。
```

###### 例4：查询数据

（1）查询部分数据

```
QString select_sql = "select id, name from student";
if(!sql_query.exec(select_sql))
{
    qDebug()<<sql_query.lastError();
}
else
{
    while(sql_query.next())
    {
        int id = sql_query.value(0).toInt();
        QString name = sql_query.value(1).toString();
        qDebug()<<QString("id:%1    name:%2").arg(id).arg(name);
    }
}
```


语句select , , … from <table_name>;
select是查询指令； 等等是要查询的变量（即表头），中间用逗号隔开；from …指定表格。
上述语句是说查询student表中的 id 和 name 。执行查询之后，用sql_query.value(int)来获得数据。同样地，value(0)表示第一个数据，即 id，value(1)表示name。注意：value()函数的返回值类型是QVariant，因此要用toInt()等函数转换成特定的类型。
（2）查询全部数据

```
QString select_all_sql = "select * from student";
sql_query.prepare(select_all_sql);
if(!sql_query.exec())
{
    qDebug()<<sql_query.lastError();
}
else
{
    while(sql_query.next())
    {
        int id = sql_query.value(0).toInt();
        QString name = sql_query.value(1).toString();
        int age = sql_query.value(2).toInt();
        qDebug()<<QString("id:%1    name:%2    age:%3").arg(id).arg(name).arg(age);
    }
}
```


语句select * from <table_name>;
查询所有数据用 * 表示。用while(sql_query.next())用来遍历所有行。同样用value()获得数据。
（3）查询最大id

```
QString select_max_sql = "select max(id) from student";
int max_id = 0;
sql_query.prepare(select_max_sql);
if(!sql_query.exec())
{
    qDebug() << sql_query.lastError();
}
else
{
    while(sql_query.next())
    {
        max_id = sql_query.value(0).toInt();
        qDebug() << QString("max id:%1").arg(max_id);
    }
}
```


这个就是在语句中用max来获取最大值。

例5：删除与清空
（1）删除一条数据

```
QString delete_sql = "delete from student where id = ?";
sql_query.prepare(delete_sql);
sql_query.addBindValue(0);
if(!sql_query.exec())
{
    qDebug()<<sql_query.lastError();
}
else
{
    qDebug()<<"deleted!";
}
```


语句delete from <table_name> where =
delete用于删除条目，用where给出限定条件。例如此处是删除 id = 0的条目。
（2）清空表格（删除所有）

```
QString clear_sql = "delete from student";
sql_query.prepare(clear_sql);
if(!sql_query.exec())
{
    qDebug() << sql_query.lastError();
}
else
{
    qDebug() << "table cleared";
```

##### 5、sqlite expert 教程

### 3、json格式封装

### 4、qt designer界面和所有功能介绍

#### 1. 界面介绍

使用tab widget可实现窗口整合

![image-20240423145256928](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240423145256928.png)

如下图所示是Qt designer的一个标准界面。按1~6的顺序简要介绍各模块：

导航栏：包括文件操作（新建、导入、保存等）、窗体（预览窗口3中的UI效果）、视图（显示or关闭各种模块窗口2~6）、设置（各种页面属性设置）等。其中最为常用且重要的是文件和窗体，选择窗体→ \rightarrow→预览（或者快捷键Ctrl+R）可以看到UI的实际运行效果。视图保持默认的全部开启即可。
Widget Box（部件盒）：Widget Box位于Qt Designer界面的左侧，它提供了各种可用的窗口部件（widgets）。可以从Widget Box中拖放这些部件到主界面编辑区域，以构建GUI界面。Widget Box中的部件包括按钮、标签、文本框、下拉列表等。
Form Editor（表单编辑器）：Form Editor是Qt Designer界面的主要编辑区域，位于中央部分。在表单编辑器中，可以拖放部件到界面上，调整它们的位置和大小，并设置它们的属性和布局。可以使用表单编辑器创建和修改GUI界面的布局，如添加布局管理器、设置部件的位置和大小等。换句话说就是你设计UI的地方。
Object Inspector（对象检查器）：Object Inspector位于Qt Designer界面的右侧，它显示了当前选中部件的属性和布局信息。可以使用对象检查器来查看和修改部件的属性，如文本、字体、大小等。它还允许设置部件的布局属性，如位置、大小、对齐方式等。
Property Editor（属性编辑器）：Property Editor位于Qt Designer界面的右方中部，默认显示在对象检查器下方。它提供了一个表格视图，显示了当前选中部件的属性列表。可以使用属性编辑器来直接编辑和修改部件的属性值。通过双击属性值可以进行编辑或选择合适的值。
窗口6包含3个模块：
Resource Browser（资源浏览器）：资源浏览器是一个用于管理和引用资源文件的功能模块。资源文件可以包含图像、字体、样式表等应用程序所需的各种资源。资源浏览器提供了一种方便的方式来添加、编辑和使用这些资源。
Signal/Slot Editor（信号/槽编辑器）：Signal/Slot Editor位于Qt Designer界面的底部，通常与属性编辑器一起显示。它提供了一个图形界面，用于管理和连接部件之间的信号和槽。通过信号/槽编辑器，可以将部件的信号与其他部件的槽函数进行连接，以实现交互和事件处理。
Action Editor（动作编辑器）：Action Editor是Qt Designer中的一个独立模块，用于管理和配置菜单栏、工具栏和上下文菜单中的动作（actions）。通过动作编辑器，可以添加、修改和删除各种动作，并为它们设置相应的属性和快捷键

#### 2、Widget Box 常用组件

##### 2.1 Layouts（布局）

Widget Box中的Layouts（布局）组件提供了一种方便的方式来管理部件的位置和大小，以及定义它们之间的关系。这些布局部件可以帮助您创建自适应和灵活的GUI界面。以下是Widget Box中常见的布局组件及其功能介绍：

QVBoxLayout（垂直布局）：VBox Layout按垂直方向排列其子部件。它们会根据容器的大小自动调整子部件的大小和位置。可以向垂直布局添加部件，并控制它们的对齐方式、间距等属性。
QHBoxLayout（水平布局）：HBox Layout按水平方向排列其子部件。类似于垂直布局，它们会根据容器的大小自动调整子部件的大小和位置。可以向水平布局添加部件，并控制它们的对齐方式、间距等属性。
QGridLayout（网格布局）：Grid Layout将部件排列为网格状结构，可以在行和列中进行布局。可以指定部件的位置，并控制它们在网格中的大小和间距。
QFormLayout（表单布局）：Form Layout适用于创建表单样式的界面，其中包含标签和对应的输入部件。它将标签和部件配对，并按照列的方式进行布局。可以控制标签和部件的对齐方式、间距等属性。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2d493497566b428082a5777eeccf1e2e.png#pic_center)

##### 2.2 Spacers（间隔器）

在Widget Box中，Spacers（间隔器）是一组组件，用于在布局中创建空白空间，以调整部件之间的距离和位置。它们有助于实现灵活的界面布局。以下是Widget Box中Spacer组件及其功能介绍：

QSpacer：QSpacer是一个简化的间隔器部件，它可以直接添加到布局中。它可以在垂直布局、水平布局或网格布局中创建空白空间。可以调整QSpacer的大小和属性，以满足您的布局需求。包括：Horizontal Spacer & Vertical Spacer。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cd7bf9398e4e487c89e4c1947bc3a955.png#pic_center)

##### 2.3 Item Views（项视图）

Widget Box中的Item Views（项视图）组件提供了用于显示和编辑结构化数据的部件，例如表格、树状结构和列表。这些组件提供了丰富的功能和灵活的选项，以满足各种数据展示和交互需求。以下是Widget Box中常见的Item Views组件及其功能介绍：

QListWidget（列表视图）：QListWidget是一个用于显示和编辑简单列表数据的部件。它以列表形式呈现数据，并支持单选和多选模式。可以添加、删除和编辑列表项，并设置其图标、文本等属性。
QTreeView（树视图）：QTreeView是一个用于显示和编辑树状结构数据的部件。它以层次结构形式展示数据，并可展开和折叠节点。可以通过设置模型和委托来自定义树视图的外观和行为。
QTableView（表格视图）：QTableView是一个用于显示和编辑二维表格数据的部件。它以表格形式呈现数据，并提供了各种功能，如排序、编辑、选择、拖放等。可以通过设置模型和委托来自定义表格的外观和行为。
QColumnView（列视图）：QColumnView是一个用于显示和编辑多列数据的部件。它以列的形式展示数据，并允许用户在列之间进行导航和选择。可以设置列视图的模型和委托来自定义其外观和行为。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0c74d7d18d64460bad3ce676a2362166.png#pic_center)

##### 2.4 Item Widgets（项部件）

在Widget Box中，Item Widgets（项部件）是一组用于在Item Views组件中显示和编辑数据的部件。与Item Views类似。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a70f7c61c9a1407fb2fd944b52d68dbe.png#pic_center)

##### 2.5 Containers（容器）

容器部件（container widget）在窗体中提供更高级的对象分组控制。它们可以作为多种用途使用，诸如管理输入部件（input widgets）、提供分页和选项卡布局、又或者仅仅作为其它对象的装饰性容器等等。以下是常见的Containers组件及其功能介绍：

Group Box （组框）：组框的主要应用场景就是将复选框和单选按钮等作为一个集合有机的进行分组。每个组框都有自己的布局，尤其当其包含部件时，布局是必需有的。同日常的布局操作一样，对组框的布局操作也没有什么特别的，只需要在其内部右键操作布局即可。
Scroll Area（滚动区域）：提供了在有限空间内显示大量内容的功能。当内容超过Scroll Area的可见区域时，它会自动添加滚动条，以便用户可以滚动查看内容，例如文本、图像、部件等。
Tool Box（工具箱） ：可以理解为，工具箱有很多抽屉，每次只能打开一个，抽屉里可以放很各种各样的东西，例如QQ的好友分组，每个分组下有不同数目的联系人。点击不同抽屉时，会触发currentChanged信号。
Tab Widget（多页面切换）：可以实现在同一区域中自由切换不同页面的内容，该控件是一个容器类控件，并提供友好的页面切换方式。
Stacked Widget（层叠窗口/堆栈窗口）：多界面切换。可以填充一些小控件，但是同一时间只有一个小控件可以显示。QStackedWidget控件不能在页面之间进行切换，它与当前选中的QListWidget控件中的选项进行连接currentRowChanged信号。
Frame（框架）：控制一些边框的样式，凸起，凹下，阴影等。
Widge：组件，主窗体容器。
MDI Area：可以同时显示多个文档的区域。
Dock Widget（浮动窗口）：它是放置在QMainWindow中的中央窗口小部件周围的停靠窗口小部件区域中的次要窗口。QDockWidget由一个标题栏和内容区域组成。标题栏显示浮动窗口小部件窗口标题，浮动按钮和关闭按钮。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/8fb4e682622c46b6acf5c910c6c32863.png#pic_center)

##### 2.6 Input Widgets

Widget Box中的Input Widgets（输入部件）提供了多种用于用户输入数据的部件。以下是这些组件的详细功能介绍：

Combo Box ：组合框。是一个集按钮和下拉选项于一体的控件，也称做下拉列表框。
Font Combo Box ：字体组合框。组合框中填充了按字母顺序排列的字体系列名称列表，让用户选择字体。
Line Edit ：单行文本框。
Text Edit ：多行文本框。可显示多行文本内容，当文本内容超出控件显示范围时，可以显示水平和垂直滚动条。
Plain Text Edit ：纯文本框。用于编辑和显示纯文本控件。在默认情况下，一个换行符表示一个段落，文档可以一个或者多个段落组成，且段落中的每个字符都可以有其自己的属性，例如有自己的字体和颜色。
Spin Box ：整型数值计数器。允许用户选择一个整数值通过单击向上向下或者按键盘上的上下键来增加减少当前显示的值，当然用户也可以输入值。
Double Spin Box ：浮点数值计数器。默认精度是两位小数，可以通过setDecimals（）来改变。
Time Edit ：时间编辑。
Date Edit ：日期编辑。
Date/Time Edit ：时间/日期编辑。
Dial ：圆表盘控件。例如汽车仪表盘上的速度计，就是一个圆表盘。
Horizontal Scroll Bar ：水平滚动条。
Vertical Scroll Bar ：垂直滚动条。
Key Sequence Edit ：用于输入快捷键序列的控件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e1de605667484144900f5742f7e15f0a.png#pic_center)

##### 2.7 Display Widgets

Widget Box中的Display Widgets（显示部件）用于在应用程序中显示和展示数据或信息。以下是这些组件的详细功能介绍：

Label : 标签。QLabel用于在应用程序中显示文本或图像。它可以显示静态文本、富文本或图像，并且支持自动换行、对齐和格式化等功能。
Text Browser : 文本浏览框。QTextBrowser用于在应用程序中显示多行文本或富文本。它提供了一个只读的多行文本显示区域，支持显示和浏览大段文本、富文本或超链接等。
Graphics View : 用于显示QGraphicsScene场景的部件，可用来显示图片。QGraphicsView框架主要包含三个主要的类QGraphicsScene（场景）、QGraphicsView（视图）、QGraphicsItem（图元）。
Calendar Widget : 日历小部件。是一个设置日历的控件，它提供了一个基于月份的视图，允许用户通过鼠标和键盘选择日期，默认是选中今天，可以对日期的范围进行限制。
LCD Number : LCD屏部件，显示LCD样式的数字。
Progress Bar : 进度条，其实就是滑块，可以设置其最小值、最大值、当前值，也可以设置其方向，如横向或者竖向。
Horizontal Line : 水平线。
Vertical Line : 垂直线。
OpenGL Widget : 是一个OpenGL（Open Graphics Library，开放图形库）图形渲染的部件，可以在PyQt和Qt的应用中显示图形（包括2D和3D图形），在Designer中，该部件没有任何独有属性，都是继承的QWidget的属性。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/91be0a0db9fa4846996482f1feda82b1.png#pic_center)
