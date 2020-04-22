tuple是一个固定大小的不同类型值的集合，是泛化的std::pair。和c#中的tuple类似，但是比c#中的tuple强大得多。我们也可以把他当做一个通用的结构体来用，不需要创建结构体又获取结构体的特征，在某些情况下可以取代结构体使程序更简洁，直观。  

参考：TuplesTest.cpp
```
#include <Poco/Format.h>
#include <Poco/Runnable.h>
#include <Poco/Thread.h>
#include <Poco/Tuple.h>
#include <Poco/NamedTuple.h>

#include <string>
#include <set>

#include "ScopedLogMessage.h"
#include "PrepareConsoleLogger.h"

// 测试setget
void TestGetSet(ScopedLogMessage& msg)
{
    msg.Message(" --- Test get/set ---");

    typedef Poco::Tuple<short, long, double> MyTupleType;

    MyTupleType aTuple(1, 2, 3.0);
    msg.Message(Poco::format("  aTuple: (%+hd, %+ld, %+4.2f), length=%i"
            ,	aTuple.get<0>()
            ,	aTuple.get<1>()
            ,	aTuple.get<2>()
            ,	static_cast<int>(aTuple.length) ));

    aTuple.set<0>(-1 * aTuple.get<0>());
    aTuple.set<1>(-1 * aTuple.get<1>());
    aTuple.set<2>(-1 * aTuple.get<2>());

    msg.Message(Poco::format("  aTuple: (%+hd, %+ld, %+4.2f), length=%i"
            ,	aTuple.get<0>()
            ,	aTuple.get<1>()
            ,	aTuple.get<2>()
            ,	static_cast<int>(aTuple.length) ));
}

// 测试排序
void TestOperatorLT(ScopedLogMessage& msg)
{
    msg.Message(" --- Test operator less than ---");

    typedef Poco::Tuple<std::string, int, long, float> MyTupleType;
    typedef std::set<MyTupleType> MyTupleSet;

    MyTupleType aTuple2("2", 2, 222, 2.22f);
    MyTupleType aTuple1("1", 1, 111, 1.11f);
    MyTupleType aTuple3("3", 3, 333, 3.33f);

    MyTupleSet testSet;
    testSet.insert(aTuple3);
    testSet.insert(aTuple2);
    testSet.insert(aTuple1);

    MyTupleSet::iterator itr = testSet.begin();
    MyTupleSet::iterator itrEnd = testSet.end();
    int count = 0;
    while(itr != itrEnd)
    {
        msg.Message(Poco::format("  testSet #%i: (\"%s\", %i, %ld, %4.2hf), length=%i"
                ,	count++
                ,	itr->get<0>()
                ,	itr->get<1>()
                ,	itr->get<2>()
                ,	itr->get<3>()
                ,	static_cast<int>(itr->length) ));
        ++itr;
    }
}

// 命名元组
void TestNamedTuple(ScopedLogMessage& msg)
{
    msg.Message(" --- Test NamedTuple ---");

    typedef Poco::NamedTuple<std::string, int, long, float> MyTupleType;

    MyTupleType aTuple("1", 1, 111, 1.11f);

    msg.Message("  names for aTuple elements:");
    for(std::size_t i=0; i<aTuple.names()->size(); ++i)
    {
        msg.Message(Poco::format("   %z: %s", i, aTuple.names()->at(i)));
    }
    msg.Message("  get elements by name:");
    msg.Message(Poco::format("   aTuple: (\"%s\", %i, %ld, %4.2hf), length=%i"
            ,	aTuple[aTuple.names()->at(0)].convert<std::string>()
            ,	aTuple[aTuple.names()->at(1)].convert<int>()
            ,	aTuple[aTuple.names()->at(2)].convert<long>()
            ,	aTuple[aTuple.names()->at(3)].convert<float>()
            ,	static_cast<int>(aTuple.length) ));
}

int main(int /*argc*/, char** /*argv*/)
{
    PrepareConsoleLogger logger(Poco::Logger::ROOT, Poco::Message::PRIO_INFORMATION);

    ScopedLogMessage msg("TupleTest ", "start", "end");

    TestGetSet(msg);
    TestOperatorLT(msg);
    TestNamedTuple(msg);

    return 0;
}
```