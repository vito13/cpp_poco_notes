https://blog.csdn.net/tianya_team/article/details/75268672?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-5&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-5


在Poco库中，封装了一些类去完成文件系统的操作，这些类包括：

              1. Poco::Path

              2. Poco::File

              3. Poco::TemporaryFile

              4. Poco::DirectoryIterator

              5. Poco::Glob

1. Poco::Path
1.1 路径：
               1. 在不同操作系统中，指明文件和目录所在位置的标示符是不一样的。
               2. 标示符的不一致，会造成代码在不同平台之间移植的困难。
               3. Poco::Path类抽象了不同标识符之间的区别，使程序员可以把注意力集中在业务的开发上。
               4. Poco::Path类支持Windows、Unix、OpenVMS操作系统。


1.2 Poco路径简介：
               Poco中的路径包括了：
               1. 一个可选的节点(node)名:
                              a) 在Windows上，这是计算机在UNC(Universal Naming Convention)路径中的名字
                              b) 在OpenVMS中，这代表一个集群系统中的节点名
                              c) 在Unix中，此名字未被使用。
               2. 一个可选的设备(device)名:
                              a) 在Windows上，这是一个驱动器盘符
                              b) 在OpenVMS上，这是存储盘符的名字
                              c) 在Unix，此名字未被使用。
               3. 一个目录名的列表
               4. 一个文件名(包括扩展名)和版本号(OpenVMS特有)

               Poco支持两种路径:
               1. 绝对路径
               以根目录为起点的描述资源的目录
               2. 相对目录
               以某一个确定路径为起点的描述资源的目录(通常这是用户的当前目录)

               相对目录可以被转换为绝对目录(反之，并不成立)。
               在Poco中路径的指向可以是一个目录也可以是一个文件。当路径指向目录时，文件名为空。

               下面是Poco中关于路径的一些例子：
    Path: C:\Windows\system32\cmd.exe  
        Style: Windows  
        Kind: absolute, to file  
        Node Name: –  
        Device Name: C  
        Directory List: Windows, system32  
        File Name: cmd.exe  
        File Version: –  
      
    Path: Poco\Foundation\  
        Style: Windows  
        Kind: relative, to directory  
        Node Name: –  
        Device Name: –  
        Directory List: Poco, Foundation  
        File Name: –  
        File Version: –  
      
    Path: \\www\site\index.html  
        Style: Windows  
        Kind: absolute, to file  
        Node Name: www  
        Device Name: –  
        Directory List: site  
        File Name: index.html  
        File Version: –  
      
    Path: /usr/local/include/Poco/Foundation.h  
        Style: Unix  
        Kind: absolute, to file  
        Node Name: –  
        Device Name: –  
        Directory List: usr, local, include, Poco  
        File Name: index.html  
        File Version: –  
      
    Path: ../bin/  
        Style: Unix  
        Kind: relative, to directory  
        Node Name: –  
        Device Name: –  
        Directory List: .., bin  
        File Name: –  
        File Version: –  
      
    Path: VMS001::DSK001:[POCO.INCLUDE.POCO]POCO.H;2  
        Style: OpenVMS  
        Kind: absolute, to file  
        Node Name: VMS001  
        Device Name: DSK001  
        Directory List: POCO, INCLUDE, POCO  
        File Name: POCO.H  
        File Version: 2


1.3 类说明
               1. Poco::Path类在Poco库中代表了路径。
               2. Poco::Path类并不关心路径所指向的目标在文件系统中是否存在。这个工作由Poco::File类负责。
               3. Poco::Path支持值语义(copy函数和赋值函数)，但不支持关系操作符。

                构建一个路径
               构建一个路径存在着两种方式：
               1. 从0开始构建，分别构建node、device、directory、file
               2. 通过一个包含着路径的字符串去解析

               在构建时，可以指定路径的格式：
                   a)PATH_UNIX
                   b)PATH_WINDOWS
                   c)PATH_VMS
                   d)PATH_NATIVE (根据当前系统格式判断)
                   e)PATH_GUESS (让Poco库自行判断)


               从0构造路径
               1. 创建一个空路径，使用默认的构造函数(默认情况下路径格式为"相对目录")或者构造时使用一个bool参数去指定路径格式(true = absolute, false = relative)
               2. 如果需要的话，使用下列赋值函数去设置节点和设备名
                              void setNode(const std::string& node)
                              void setDevice(const std::string& device)
               3. 添加路径名
                              void pushDirectory(const std::string& name)
               4. 设置文件名
                              void setFileName(const std::string& name)

               下面是一个例子：

    #include "Poco/Path.h"
    using namespace std;
    int main(int argc, char** argv)
    {
        Poco::Path p(true); // path will be absolute  
        p.setNode("VMS001");
        p.setDevice("DSK001");
        p.pushDirectory("POCO");
        p.pushDirectory("INCLUDE");
        p.pushDirectory("POCO");
        p.setFileName("POCO.H");
        std::string s(p.toString(Poco::Path::PATH_VMS));
        // "VMS001::DSK001:[POCO.INCLUDE.POCO]POCO.H"  
        p.clear(); // start over with a clean state  
        p.pushDirectory("projects");
        p.pushDirectory("poco");
        s = p.toString(Poco::Path::PATH_WINDOWS); // "projects\poco\" 
        cout<<s<<endl;
        s = p.toString(Poco::Path::PATH_UNIX); // "projects/poco/"  
        s = p.toString(); // depends on your platform  
        cout<<s<<endl;
        return 0;
    }


               从一个字符串中解析路径名
               1. Poco支持从一个字符串中解析路径名
                              Path(const std::string& path)
                              Path(const std::string& path, Style style)
               如果函数调用时，路径格式style不被指定，将使用当前系统路径格式。
               2. 可以从另一个路径(指向目录名)和文件名，或者两个路径(第一个为绝对路径，第二个为相对路径)构造
                              Path(const Path& parent, const std::string& fileName)
                              Path(const Path& parent, const Path& relative)

               路径也可以通过下列函数去构建
                              Path& assign(const std::string& path)
                              Path& parse(const std::string& path)
                              Path& assign(const std::string& path, Style style)
                              Path& parse(const std::string& path, Style style)

               如果路径非法的话，会抛出Poco::PathSyntaxException异常。想要测试一个路径字符串是否合法，可以使用tryParse()函数：
                              bool tryParse(const std::string& path)
                              bool tryParse(const std::string& path, Style style)

               下面是一个例子：

    #include "Poco/Path.h"  
    using Poco::Path;  
    int main(int argc, char** argv)  
    {  
        //creating a path will work independent of the OS  
        Path p("C:\\Windows\\system32\\cmd.exe");  
        Path p("/bin/sh");  
        p = "projects\\poco";  
        p = "projects/poco";  
        p.parse("/usr/include/stdio.h", Path::PATH_UNIX);  
        bool ok = p.tryParse("/usr/*/stdio.h");  
        ok = p.tryParse("/usr/include/stdio.h", Path::PATH_UNIX);  
        ok = p.tryParse("/usr/include/stdio.h", Path::PATH_WINDOWS);  
        ok = p.tryParse("DSK$PROJ:[POCO]BUILD.COM", Path::PATH_GUESS);  
        return 0;  
    }


               Poco::Path类提供了函数用于转换成为字符串：
               std::string toString()
               std::string toString(Style style)
               当然也可以使用下列函数得到路径不同部分的字符串：
              const std::string& getNode()
              const std::string& getDevice()
              const std::string& directory(int n) (also operator [])
              const std::string& getFileName()
               可以调用下列函数获取目录的深度:
               int depth() const

               通过下面的函数可以得到和设置文件的基本名和扩展名:
                              std::string getBaseName() const
                              void setBaseName(const std::string& baseName)
                              std::string getExtension() const
                              void setExtension(const std::string& extension)

               下面是一个例子:

    #include "Poco/Path.h"  
    using Poco::Path;  
    int main(int argc, char** argv)  
    {  
        Path p("c:\\projects\\poco\\build_vs80.cmd", Path::PATH_WINDOWS);  
        std::string device(p.getDevice()); // "c"  
        int n = p.depth(); // 2  
        std::string dir1(p.directory(0)); // "projects"  
        std::string dir2(p[1]); // "poco"  
        std::string fileName(p[2]); // "build_vs80.cmd"  
        fileName = p.getFileName();  
        std::string baseName(p.getBaseName()); // "build_vs80"  
        std::string extension(p.getExtension()); // "cmd"  
        p.setBaseName("build_vs71");  
        fileName = p.getFileName(); // "build_vs71.cmd"  
        return 0;  
    } 


               路径操作:
               1. Path& makeDirectory()
               确保路径的结尾是一个目录名。如果原路径有文件名存在的话，添加一个与文件名同名的目录，并清除文件名。
               2. Path& makeFile()
               确保路径的结尾是一个文件名。如果原路径是一个目录名，则把最后一个目录名变成文件名，并去除最后一个目录名。
               3. Path& makeParent()
                   Path parent() const
               使路径指向它的父目录(如果存在文件名的话，清除文件名；否则的话则移除最后一个目录名)
               4. Path& makeAbsolute()
                   Path& makeAbsolute(const Path& base)
                   Path absolute() const
                   Path absolute(const Path& base)
               转换相对路径为绝对路径
               5. Path& append(const Path& path)
               添加路径
               6. Path& resolve(const Path& path)
# 仔细观察函数resolve的作用
```
        
        Path p("/1/2/3/stdio.h", Path::PATH_UNIX); 绝对
        Path p2("../4/5/a.txt", Path::PATH_UNIX); 相对
        p.resolve(p2);
        std::string s1 = p.toString(Path::PATH_UNIX);    // "/1/2/4/5/a.txt"


        Path p("/1/2/3/stdio.h", Path::PATH_UNIX); 绝对
        Path p2("a/4/5/a.txt", Path::PATH_UNIX); 相对
        p.resolve(p2);
        std::string s1 = p.toString(Path::PATH_UNIX);   // "/1/2/3/a/4/5/a.txt"


        Path p("/1/2/3/stdio.h", Path::PATH_UNIX); 绝对
        Path p2("/a/4/5/t.ini", Path::PATH_UNIX); 绝对
        p.resolve(p2);
        std::string s1 = p.toString(Path::PATH_UNIX);   // "/a/4/5/t.ini"


        Path p("../1/2/3/stdio.h", Path::PATH_UNIX); 绝对或相对，如/1/2/3/stdio.h或stdio.h
        Path p2("/a/4/5/t.ini", Path::PATH_UNIX);
        p.resolve(p2);
        std::string s1 = p.toString(Path::PATH_UNIX);  // 结果都是"/a/4/5/t.ini"


        Path p("../1/2/3/stdio.h", Path::PATH_UNIX); 两个都是相对
        Path p2("../a/4/5/t.ini", Path::PATH_UNIX);
        p.resolve(p2);
        std::string s1 = p.toString(Path::PATH_UNIX);  // "../1/2/a/4/5/t.ini"
```               
               
               ，则使用参数的文件名替换现有的文件名，如果参数是绝对路径，则代替现有的路径；否则则在原路径下追加

               路径属性：
               1. bool isAbsolute() const
               如果路径为绝对路径，返回true；否则为false
               2. bool isRelative() const
               如果路径为相对路径，返回true；否则为false
               3. bool isDirectory() const
               如果路径为目录，返回true；否则为false
               4. bool isFile() const
               如果路径为文件，返回true；否则为false


               下面是一个例子：

    #include "Poco/Path.h"  
    using Poco::Path;  
    int main(int argc, char** argv)  
    {  
        Path p("/usr/include/stdio.h", Path::PATH_UNIX);  
        Path parent(p.parent());  
        std::string s(parent.toString(Path::PATH_UNIX)); // "/usr/include/"  
        Path p1("stdlib.h");  
        Path p2("/opt/Poco/include/Poco.h", Path::PATH_UNIX);  
        p.resolve(p1);  
        s = p.toString(Path::PATH_UNIX); // "/usr/include/stdlib.h"  
        p.resolve(p2);  
        s = p.toString(Path::PATH_UNIX); // "/opt/Poco/include/Poco.h"  
        return 0;  
    }  


               特殊的目录和文件
               Poco::Path提供了静态函数，用于获取系统中的一些特殊目录和文件
               1. std::string current()
               返回当前的工作目录
               2. std::string home()
               返回用户的主目录
               3. std::string temp()
               返回操作系统的零时目录
               4. std::string null()
               返回系统的空目录(e.g., "/dev/null" or "NUL:")

               下面是一个例子：

    #include "Poco/Path.h"  
    #include <iostream>  
    using Poco::Path;  
    int main(int argc, char** argv)  
    {  
        std::cout  
            << "cwd: " << Path::current() << std::endl  
            << "home: " << Path::home() << std::endl  
            << "temp: " << Path::temp() << std::endl  
            << "null: " << Path::null() << std::endl;  
        return 0;  
    } 


               路径和环境变量
               在配置文件中的路径经常包含了环境变量。在传递此类路径给Poco::Path之间必须对路径进行扩展。
               对包含环境变量的路径扩展可以使用如下函数
                               std::string expand(const std::string& path)
               函数会返回一个对环境变量进行扩充后的路径名。环境变量的格式会根据操作系统有所不同。(e.g., $VAR on Unix, %VAR% on Windows).在Unix上，同样会扩展"~/"为当前用户的主目录。

               下面是一个例子：
# 扩展环境变量函数expand
    #include "Poco/Path.h"  
    using Poco::Path;  
    int main(int argc, char** argv)  
    {  
        std::string config("%HOMEDRIVE%%HOMEPATH%\\config.ini");   windows
        std::string config("$HOME/config.ini");    linux
        std::string expConfig(Path::expand(config));  
        return 0;  
    }


               文件系统主目录:
               void listRoots(std::vector<std::string>& roots)
               会用所有挂载到文件系统的根目录来填充字符串数组。在windows上，为所有的驱动盘符。在OpenVMS上，是所有挂载的磁盘。在Unix上，为"/"。

               查询文件:
               bool find(const std::string& pathList, const std::string& name, Path& path)
               在指定的目录集(pathList)中搜索指定名称的文件(name)。pathList参数中如果指定了多个查找目录，目录之间必须使用分割符 (Windows平台上为";" , Unix平台上为":")。参数name中可以包含相对路径。如果文件在pathList指定的目录集中存在，找到文件的绝对路径会被放入path参数中，并且函数返回true，否则函数返回false，并且path不改变。
               这个函数也存在一个使用字符串向量，而不是路径列表的迭代器的变体。定义如下：
               bool find(StringVec::const_iterator it, StringVec::const_iterator end, const std::string& name, Path& path)

               下面是一个例子：
# 使用环境变量path查找文件路径
    #include "Poco/Path.h"  
    #include "Poco/Environment.h"  
    using Poco::Path;  
    using Poco::Environment;  
    int main(int argc, char** argv)  
    {  
        std::string shellName("cmd.exe"); // Windows  
        // std::string shellName("sh"); // Unix  
        std::string path(Environment::get("PATH"));  
        Path shellPath;  
        bool found = Path::find(path, shellName, shellPath);  
        std::string s(shellPath.toString());  
        return 0;  
    } 