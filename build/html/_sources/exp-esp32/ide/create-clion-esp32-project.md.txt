# 从零开始创建一个 ESP32 的 Clion 工程的正确姿势

要从空白开始实现一个项目，需要两个工具，一个就是基于命令行的 idf.py 工具，另一个就是编码，调试使用的 Clion 集成开发环境。

## 使用 idf.py 创建和配置工程

```
get-idf # 或缺idf开发所需的工具
## 熟悉下这个idf.py 有什么功能
idf.py help
## 学的差不多开是创建项目
## 在你的代码文件夹创建一个项目，比方blinker
mkdir ~/code/esp32/
cd  ~/code/esp32/
idf.py create-project
## ls 下，看看发生了什么，多出那些文件
idf.py set-target esp32
## ls 下在看看发生了什么
idf.py menuconfig
## ls 下 看看发生了什么，这个步骤也可以移到clion中做。
idf.py build
........
## 结束后在ls 下看又发生了什么。

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
