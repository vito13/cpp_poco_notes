# 概述
URI(RFC 3986)意为统一资源标记，通常被用来标志web上的资源。
在Poco库中提供了POCO::URI、POCO::URIStreamFactory、POCO::URIStreamOpener类来对URI信息进行管理。其中POCO::URI用于进行URI操作和存储。URIStreamFactory可以打开一个URI资源，并且把该URI资源和一个输入流相关联。URIStreamOpener类用来设计成对URIStreamFactory进行管理。通过URIStreamFactory和URIStreamOpener可以把对所有资源的读取都适配一个流接口。

# POCO::URI
一个URI标志通常包括下列部分：
Scheme：协议
Authority：包括了主机地址、端口、用户信息(通常指用户名/密码)
Path:  路径
Query:  查询
Fragment: 内部资源地址

下面是URI的一些例子：
```
http    ://    www.google.com    /    search    ?    q=POCO
 |                  |                   |               |
 |                  |                   |               |
Scheme             Host                Path           Query



http    ://    appinf.com    /    poco/docs/Poco.URI.html    #    5589
 |                  |                       |                      |
 |                  |                       |                      |
Scheme             Host                    Path                 Fragment 


ftp    ://    anonymous    @    upload.sourceforge.com    /    incoming
 |                  |                       |                      |
 |                  |                       |                      |
Scheme             User                    Host                   Path
```
POCO::URI类可以被看成是URI标记的集合。在POCO::URI内部单独定义了不同字段来对应存储URI的不同部分。值得注意到是，在POCO::URI中存在两个函静态数encode()和decode()。这两个函数用来对URI资源进行Percent-encoding编码。这是由于在URI标准中定义了一些保留字符，如果URI资源中存在保留字符，必须对它们进行转义。

```
#include "Poco/URI.h"
#include <iostream>
 
int main(int argc, char** argv)
{
	Poco::URI uri1("http://www.appinf.com:88/sample?example-query#frag");
	std::string scheme(uri1.getScheme()); // "http"
	std::string auth(uri1.getAuthority()); // "www.appinf.com:88"
	std::string host(uri1.getHost()); // "www.appinf.com"
	unsigned short port = uri1.getPort(); // 88
	std::string path(uri1.getPath()); // "/sample"
	std::string query(uri1.getQuery()); // "example-query"
	std::string frag(uri1.getFragment()); // "frag"
	std::string pathEtc(uri1.getPathEtc()); // "/sample?examplequery#frag"
 
	Poco::URI uri2;
	uri2.setScheme("https");
	uri2.setAuthority("www.appinf.com");
	uri2.setPath("/another sample");
	std::string s(uri2.toString());
	// "https://www.appinf.com/another%20sample"
 
	Poco::URI uri3("http://www.appinf.com");
	uri3.resolve("/poco/info/index.html");
	s = uri3.toString(); // "http://www.appinf.com/poco/info/index.html"
	uri3.resolve("support.html");
	s = uri3.toString(); // "http://www.appinf.com/poco/info/support.html"
	uri3.resolve("http://sourceforge.net/projects/poco");
	s = uri3.toString(); // "http://sourceforge.net/projects/poco"
	return 0;
}
```

# POCO::URIStreamOpener
POCO::URIStreamOpener类主要用于为URI资源，创建并打开一个对应的输入流。对于每一种URI协议，Poco::URIStreamFactory的子类必须向POCO::URIStreamOpener注册。
在Poco库中，内置了文件(包括网络文件) 流工厂类FileStreamFactory，HTTP流工厂类 HTTPStreamFactory，FTP流工厂类 FTPStreamFactory。


案例待修正与完善
https://blog.csdn.net/arau_sh/article/details/8698463
http://poco.roundsquare.net/2010/06/04/pocouristreamopener/