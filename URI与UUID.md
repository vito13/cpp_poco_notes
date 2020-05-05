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
## 简单案例
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
## 另一个更加丰富的分解URI案例
URIParser.h
```
#include <string>
#include <vector>
#include <map>
#include <memory>
 
class URIParser
{
public:
	URIParser(const std::string& urlStr);
	~URIParser();
	int GetURL(std::string& url) const;
 
	const std::string& getScheme() const;
	const std::string& getUserInfo() const;
	const std::string& getHost() const;
	unsigned short getPort() const;
	std::string getAuthority() const;
	const std::string& getPath() const;
	std::string getQuery() const;
	const std::string& getFragment() const;
 
	void getPathSegments(std::vector<std::string>& segments);
	void getQueryMap(std::map<std::string, std::string>& keyValues) const;
 
private:
	URIParser();
	URIParser(const URIParser&);
	URIParser& operator = (const URIParser&);
 
	class URIParserImpl;
	std::auto_ptr<URIParserImpl>	m_pImpl;
};
```
URIParser.cpp
```
#include "URIParser.h"
 
#include <Poco/URI.h>
#include <Poco/StringTokenizer.h>
 
class URIParser::URIParserImpl : public Poco::URI
{
public:
	URIParserImpl(const std::string& urlStr) :
		Poco::URI(urlStr)
	{
	}
	~URIParserImpl()
	{
	}
	void getQueryMap(std::map<std::string, std::string>& keyValues) const
	{
		if(empty())	return;
 
		std::string queryString(getQuery());
		if("" == queryString)	return;
 
		Poco::StringTokenizer restTokenizer(queryString, "&");
		for(Poco::StringTokenizer::Iterator itr=restTokenizer.begin(); itr!=restTokenizer.end(); ++itr)
		{
			Poco::StringTokenizer keyValueTokenizer(*itr, "=");
			keyValues[keyValueTokenizer[0]] = (1 < keyValueTokenizer.count()) ? keyValueTokenizer[1]:"";
		}
	}
 
private:
	URIParserImpl();
	URIParserImpl(const URIParserImpl&);
	URIParserImpl& operator = (const URIParserImpl&);
};
 
URIParser::URIParser(const std::string& urlStr) :
	m_pImpl(new URIParserImpl(urlStr))
{
}
 
URIParser::~URIParser()
{
}
 
const std::string& URIParser::getScheme() const
{
	return m_pImpl->getScheme();
}
 
const std::string& URIParser::getUserInfo() const
{
	return m_pImpl->getUserInfo();
}
 
const std::string& URIParser::getHost() const
{
	return m_pImpl->getHost();
}
 
unsigned short URIParser::getPort() const
{
	return m_pImpl->getPort();
}
 
std::string URIParser::getAuthority() const
{
	return m_pImpl->getAuthority();
}
 
const std::string& URIParser::getPath() const
{
	return m_pImpl->getPath();
}
 
std::string URIParser::getQuery() const
{
	return m_pImpl->getQuery();
}
 
const std::string& URIParser::getFragment() const
{
	return m_pImpl->getFragment();
}
 
void URIParser::getPathSegments(std::vector<std::string>& segments)
{
	m_pImpl->getPathSegments(segments);
}
 
void URIParser::getQueryMap(std::map<std::string, std::string>& keyValues) const
{
	m_pImpl->getQueryMap(keyValues);
}
```
URIParserTest.cpp
```
#include <Poco/Format.h>
 
#include "ScopedLogMessage.h"
#include "PrepareConsoleLogger.h"
#include "URIParser.h"
 
//----------------------------------------
//	ParseURI
//----------------------------------------
void ParseURI(const std::string& sourceUri)
{
	ScopedLogMessage msg("ParseURI ", "start", "end");
 
	msg.Message(Poco::format(" sourceUri = %s", sourceUri));
 
	URIParser parser(sourceUri);
 
	msg.Message(Poco::format("    scheme = %s", parser.getScheme()));
	msg.Message(Poco::format("  userInfo = %s", parser.getUserInfo()));
	msg.Message(Poco::format("      host = %s", parser.getHost()));
	msg.Message(Poco::format("      port = %hu", parser.getPort()));
	msg.Message(Poco::format(" authority = %s", parser.getAuthority()));
	msg.Message(Poco::format("      path = %s", parser.getPath()));
 
	std::vector<std::string> pathSegments;
	parser.getPathSegments(pathSegments);
	for(std::vector<std::string>::iterator itr=pathSegments.begin(); itr!=pathSegments.end(); ++itr)
	{
		int count = 0;
		msg.Message(Poco::format("             path[%d] = %s", count++, *itr));
	}
 
	msg.Message(Poco::format("     query = %s", parser.getQuery()));
 
	std::map<std::string, std::string> keyValues;
	parser.getQueryMap(keyValues);
	if(0 != keyValues.size())
	{
		int count = 0;
		for(std::map<std::string, std::string>::iterator itr=keyValues.begin(); itr!=keyValues.end(); ++itr)
		{
			msg.Message(Poco::format("             query[%d] = (%s, %s)", count++, itr->first, itr->second));
		}
	}
	msg.Message(Poco::format("  fragment = %s", parser.getFragment()));
}
 
//----------------------------------------
//	main
//----------------------------------------
int main(int /*argc*/, char** /*argv*/)
{
	PrepareConsoleLogger logger(Poco::Logger::ROOT, Poco::Message::PRIO_INFORMATION);
 
	ParseURI("http://www.google.co.jp/search?source=ig&hl=ja&rlz=&q=POCO+Fanatic&meta=lr%3D&aq=f&aqi=&aql=&oq=&gs_rfai=");
 
	return 0;
}
```


# POCO::URIStreamOpener
POCO::URIStreamOpener类主要用于为URI资源，创建并打开一个对应的输入流。对于每一种URI协议，Poco::URIStreamFactory的子类必须向POCO::URIStreamOpener注册。
在Poco库中，内置了  
1. 文件(包括网络文件) 流工厂类FileStreamFactory
2. HTTP流工厂类 HTTPStreamFactory
3. FTP流工厂类 FTPStreamFactory
# 读取http网络文件、ftp文件以及临时文件的案例
```
#include <Poco/URI.h>
#include <Poco/Format.h>
#include <Poco/URIStreamOpener.h>
#include <Poco/Net/HTTPStreamFactory.h>
#include <Poco/Net/FTPStreamFactory.h>
#include <Poco/StreamCopier.h>
#include <Poco/TemporaryFile.h>
#include <Poco/Path.h>
#include <Poco/Thread.h>

#include <sstream>
#include <fstream>

#include "ScopedLogMessage.h"
#include "PrepareConsoleLogger.h"

void TestURIStreamOpener(ScopedLogMessage& msg, const Poco::URI& uri)
{
    msg.Message(Poco::format(" --- %s ---", uri.toString()));
    if("ftp" == uri.getScheme())
    {
        std::string password;
        std::string user(uri.getUserInfo());
        if("" == user)
        {
            user = "anonymous";
            password = Poco::Net::FTPStreamFactory::getAnonymousPassword();
        }
        else
        {
            Poco::Net::FTPPasswordProvider* pProvider = Poco::Net::FTPStreamFactory::getPasswordProvider();
            if(NULL != pProvider)
            {
                password = pProvider->password(user, uri.getAuthority());
            }
        }
        msg.Message(Poco::format("     (user=%s, password=%s)", user, password));
    }
    try
    {
        std::auto_ptr<std::istream> pStr(Poco::URIStreamOpener::defaultOpener().open(uri));
        std::stringstream ss;
        Poco::StreamCopier::copyStream(*pStr.get(), ss);

        Poco::Thread* p_thread = Poco::Thread::current();
        int threadID = (0 == p_thread) ? 0:p_thread->id();

        msg.Message(Poco::format(" <start>\n%s[%d]  <end>", ss.str(), threadID));
    }
    catch(Poco::Exception& exc)
    {
        msg.Message(Poco::format("    %s", exc.displayText()));
    }
}

int main(int /*argc*/, char** /*argv*/)
{
    PrepareConsoleLogger logger(Poco::Logger::ROOT, Poco::Message::PRIO_INFORMATION);

    ScopedLogMessage msg("URIStreamOpenerTest ", "start", "end");

    Poco::Net::HTTPStreamFactory::registerFactory();
    Poco::Net::FTPStreamFactory::registerFactory();

    // HTTP
    Poco::URI uriHTTP("http://poco.roundsquare.net/downloads/test.txt");
    TestURIStreamOpener(msg, uriHTTP);

    // FTP
    Poco::URI uriFTP("ftp://ftp.iij.ad.jp/pub/linux/debian/dists/README");
    TestURIStreamOpener(msg, uriFTP);

    // file
    Poco::TemporaryFile tempFile;
    std::string path = tempFile.path();
    std::ofstream ostr(path.c_str());
    ostr << "Hello, world!" << std::endl;
    ostr.close();
    Poco::URI uriFile;
    uriFile.setScheme("file");
    uriFile.setPath(Poco::Path(path).toString(Poco::Path::PATH_UNIX));
    TestURIStreamOpener(msg, uriFile);

    return 0;
}
```
# UUID
通用唯一识别码（英语：Universally Unique Identifier，简称UUID）是一种软件建构的标准。UUID的目的，是让分散式系统中的所有元素，都能有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。一组UUID，是由一串16位组（亦称128位）的16进位数字所构成，是故UUID理论上的总数为216 x 8=2128，约等于3.4 x 1038。UUID的标准型式包含32个16进位数字，以连字号分为五段，形式为8-4-4-4-12的36个字符。Poco中提供了Poco::UUID类来存储UUID信息，提供了Poco::UUIDGenerator类生成UUID信息。  
Poco::UUID类很简单，用于存储UUID信息，支持所有的值语义，包括所有的关系运算符，也支持字符串之间的相互准换。  
Poco::UUIDGenerator类用于生成UUID信息。UUIDGenerator类生成UUID信息的方法有3中，分别是基于时间生成、基于名字(数字摘要)生成、基于随机数生成。
```
#include <Poco/Format.h>
#include <Poco/UUID.h>
#include <Poco/UUIDGenerator.h>
#ifndef POCO_VERSION
#include <Poco/Version.h>
#endif

#include <string>

#include "ScopedLogMessage.h"
#include "PrepareConsoleLogger.h"

void ShowUUID(ScopedLogMessage& msg, const Poco::UUID& uuid, const std::string& text)
{
    msg.Message(Poco::format("%s %s [version=%d]"
            , text
            , uuid.toString()
            , static_cast<int>(uuid.version())));
}

int main(int /*argc*/, char** /*argv*/)
{
    PrepareConsoleLogger logger(Poco::Logger::ROOT, Poco::Message::PRIO_INFORMATION);

    ScopedLogMessage msg("UUIDTest ", "start", "end");

    msg.Message("--- Poco::UUID static methods ---");
    ShowUUID(msg, Poco::UUID::dns(),  "  dns()  :");
#if (POCO_VERSION < 0x01040000)
    ShowUUID(msg, Poco::UUID::nil(),  "  nil()  :");
#else
    ShowUUID(msg, Poco::UUID::null(), "  null() :");
#endif
    ShowUUID(msg, Poco::UUID::oid(),  "  oid()  :");
    ShowUUID(msg, Poco::UUID::uri(),  "  uri()  :");
    ShowUUID(msg, Poco::UUID::x500(), "  x500() :");

    const int kNumGeneratorLoop = 2;
    const std::string kCreateFromName("test");

    for(int i=0; i<kNumGeneratorLoop; ++i)
    {
        msg.Message(Poco::format("--- Poco::UUIDGenerator (%d of %d) ---", i+1, kNumGeneratorLoop));
        Poco::UUID uuid;
        try
        {
            uuid = Poco::UUIDGenerator::defaultGenerator().create();
            ShowUUID(msg, uuid, "  create()       :");
        }
        catch(Poco::Exception& exc)
        {
            msg.Message(Poco::format("  create() throwed exception: %s", exc.displayText()));
        }
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createOne(), "  createOne()    :");
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createRandom(), "  createRandom() :");
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createFromName(Poco::UUID::dns(), kCreateFromName)
                , "  createFromName(dns(),  \""+kCreateFromName+"\") :");
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createFromName(Poco::UUID::oid(), kCreateFromName)
                , "  createFromName(oid(),  \""+kCreateFromName+"\") :");
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createFromName(Poco::UUID::uri(), kCreateFromName)
                , "  createFromName(uri(),  \""+kCreateFromName+"\") :");
        ShowUUID(msg, Poco::UUIDGenerator::defaultGenerator().createFromName(Poco::UUID::x500(), kCreateFromName)
                , "  createFromName(x500(), \""+kCreateFromName+"\") :");
    }

    return 0;
}
```