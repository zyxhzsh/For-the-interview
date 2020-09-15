- [项目结构](#项目结构)
- [](#)
- [](#)
- [](#)
- [](#)

#### 项目结构

Project下有多个module，module下有很多个Package，Package里有多个文件。

module下有一个src文件夹，所有的代码必须写在src文件夹内。

module下还有一个iml，与开发不太相关，是idea的配置信息。

在module外面有一个External Libraries，里面是jdk，一般用不上但要有。

remove module时，module还在文件夹中。若要导入已有模块，选择file-->project structure->modules。然后点+号，import module。

包的命名：一般只用小写英文字母、数字和英文句点。比如用所在公司的域名或网址倒过来。如cn.itcast.day15.demo01。

包是一组文件夹的名称，cn包含了itcast，itcast包含了day15，day15包含了demo01。这是一个多层级文件夹的嵌套，而.起到分隔的作用。

#### HelloWorld

新建一个项目叫first,新建一个module叫lily，在module的src下新建一个包叫cn.itcast.day15.demo01。右键包新建一个java类HelloWorld。输入main或者psv后按回车，main方法就自动出来了。

点击右键，选择run'HelloWorld.main()'。结果会显示在屏幕下方，蓝色的字不是打印输出是，是一些提示。

IDEA把.class自动生成在了项目下的out文件夹里。

IDEA不需要保存，写的时候就已经保存了。
```java
package cn.itcast.day15.demo01;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

#### IDEA基本设置

IntelliJ IDEA-->Preferences

字体：Editor-->Font

自动补全：KeyMap-->keymap下有个小齿轮，点开Duplicate复制一份设置，这样不会影响本来的设置。
然后点Main menu-->Code-->Completion-->Basic，设置Basic的快捷键。

#### IDEA常用快捷键

尝试进行错误修复：option+回车

删除光标所在行：command+X

复制行：command + D   

自动给对象分配变量名：control+enter 

格式化：command+option+L

移动行：shift+command+up/down
