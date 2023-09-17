# 从零开始创建一个 ESP32 的 Clion 工程的正确姿势

要从空白开始实现一个项目，需要两个工具，一个就是基于命令行的 idf.py 工具，另一个就是编码，调试使用的 Clion 集成开发环境。

## 使用 idf.py 创建和配置工程

```
# 获取idf开发所需的环境变量与idf工具
get-idf
## 熟悉下这个idf.py 有什么功能
idf.py help
## 在你的代码文件夹创建一个项目，例如blinker
mkdir ~/code/esp32/
cd  ~/code/esp32/
idf.py create-project blinker
## 看看发生了什么，多出那些文件
tree -L 3
#设置目标芯片
idf.py set-target esp32
## 在看看目录下面发生了什么
tree -L 3
# 配置menuconfig ,
idf.py menuconfig
## 看看目录下发生了什么，考虑menuconfig的作用是什么。这个步骤也可以移到clion中做。
tree -L 3
# 编译或者构建
idf.py build
........
## 结束后在看又发生了什么。
tree -L 3

```

## 使用 Clion 管理项目

- 打开 Clion,如下图操作：

![clion—start](../IMG/clion-start.gif)

若看不清动图，请尝试观看一下视频：

<video src="../IMG/clion-start.mov"></video>

简单的总结上面动画的要点：

1. 打开 clion，然后依次`文件` -> `打开`
2. 在对话框找到你刚才创建项目的目录
3. 点击`cmakelists.txt`这个文件打开
4. 在弹出的对话框选择`作为项目打开`
   之后，你就可以摆脱开命令行，在 clion 的图形界面上愉快的编程了。
