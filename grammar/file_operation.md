1.输出数据到文件的步骤：

1）包含头文件stream

2）建立ofstream对象：ofstream ocout;

3）将对象与文件关联：ocout.open("123.txt");

4）使用该对象输出数据：ocout<<"abc";

5）关闭与文件的连接：ocout.close;

2.读取文件中数据的步骤：

1）包含头文件stream

2）建立ifstream对象：ifstream icin;

3）将对象与文件关联：icin.open("123.txt");

4）使用该对象输入数据：icin>>temp;

5）关闭与文件的连接：icin.close;

 

3.将数据输出到文件时，可以选择两种输出方式：

1）二进制：空间小，速度快，精度高

2）文件：便于阅读，可直接编辑，可移植性强

 

18.同时打开文件的数目跟所操作系统而定，一般20个左右