# Windows下编辑.bat文件

| Command | Usage |
|:-:|:-|
|echo|输出命令后面紧接着的字符,若该字符为空，则输出echo的状态（on/off）|
|@|置于命令前面，表示不显示该命令行|
|echo on|该命令后的所有命令会显示命令行本身（默认情况下会显示命令行）|
|echo off|该命令后的所有命令不会显示命令行本身|
|cls|清屏|
|title|批处理的标题|
|color|设置命令行的前景色和背景色（eg: color 02【前景色为绿色，背景色为黑色】，色彩参数见***注释1**）|
|pause|等待按键，显示"Press any key to continue..."|
|pause > nul|等待按键，不显示"Press any key to continue..."|
|rem/::|.bat文件注释，不打印在屏幕中|
|echo=|注意echo与=之间无空格，用于输出空行|
|mode con cols=宽度 lines=高度|设置窗口宽高（eg: mode con: cols=80 lines=80 / mode con cols=56 lines=80）|



**注释：**

1. 颜色：0 黑色，1蓝色，2 绿色，3 浅绿色，4红色，5紫色，6黄色，7白色，8灰色，9浅蓝，A浅绿，B浅蓝色，C浅红色，D浅紫色，E浅黄色，F亮白色
