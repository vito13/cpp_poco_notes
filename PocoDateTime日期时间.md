# 日期时间
在Poco库中，与时间和日期相关的一些类，其内部实现是非常简单的。看相关文档时，比较有意思的倒是历史上的不同时间表示法。这是经常用的知识点：
```
#include "Poco/LocalDateTime.h"
#include "Poco/DateTime.h"
#include "Poco/DateTimeFormat.h"
#include "Poco/DateTimeFormatter.h"
#include "Poco/DateTimeParser.h"
#include <iostream>


using Poco::LocalDateTime;
using Poco::DateTime;
using Poco::DateTimeFormat;
using Poco::DateTimeFormatter;
using Poco::DateTimeParser;


int main(int argc, char** argv)
{
    LocalDateTime now;
    std::cout<<"年："<<now.year()<<std::endl;
    std::cout<<"月："<<now.month()<<std::endl;
    std::cout<<"日："<<now.day()<<std::endl;
    std::cout<<"时："<<now.hour()<<std::endl;
    std::cout<<"分："<<now.minute()<<std::endl;
    std::cout<<"秒："<<now.second()<<std::endl;

   std::cout<<"本周中的第d%天："<<now.dayOfWeek()<<std::endl;
   std::cout<<"本年中的第d%天："<<now.dayOfYear()<<std::endl;
   std::cout<<"儒略日："<<now.julianDay()<<std::endl;

    std::string str = DateTimeFormatter::format(now, DateTimeFormat::ISO8601_FORMAT);
    std::cout<<"标准格式时间："<<str<<std::endl;

    std::string str_http = DateTimeFormatter::format(now, DateTimeFormat::HTTP_FORMAT);
    std::cout<<"http格式时间："<<str_http<<std::endl;

    std::string str_asctime = DateTimeFormatter::format(now, DateTimeFormat::ASCTIME_FORMAT);
    std::cout<<"ANSI格式时间："<<str_asctime<<std::endl;

    std::string str_simple = DateTimeFormatter::format(now, DateTimeFormat::SORTABLE_FORMAT);
    std::cout<<"简明格式时间："<<str_simple<<std::endl;


    DateTime dt;
    int tzd;
    DateTimeParser::parse(DateTimeFormat::ISO8601_FORMAT, str, dt, tzd);
    dt.makeUTC(tzd);
    LocalDateTime ldt(tzd, dt);

    return 0;
}
```
处理日期和时间的Poco :: DateTime  
处理时区的Poco :: Timezone  
处理日期和时间格式的Poco :: DateTimeFormatter  
另一个案例
```
#include <Poco/DateTime.h>
#include <Poco/Timezone.h>
#include <Poco/Format.h>
#include <Poco/DateTimeFormatter.h>

#include <string>

#include "ScopedLogMessage.h"
#include "PrepareConsoleLogger.h"

void DisplayDateTime(const Poco::DateTime& dateTime, ScopedLogMessage& msg)
{
    msg.Message(Poco::format("            year = %d", dateTime.year()));
    msg.Message(Poco::format("           month = %d\t(1 to 12)", dateTime.month()));
    msg.Message(Poco::format("             day = %d\t(1 to 31)", dateTime.day()));
    msg.Message(Poco::format("            hour = %d\t(0 to 23)", dateTime.hour()));
    msg.Message(Poco::format("          minute = %d\t(0 to 59)", dateTime.minute()));
    msg.Message(Poco::format("          second = %d\t(0 to 59)", dateTime.second()));
    msg.Message(Poco::format("     millisecond = %d\t(0 to 999)", dateTime.millisecond()));
    msg.Message(Poco::format("     microsecond = %d\t(0 to 999)", dateTime.microsecond()));
    msg.Message(Poco::format("            isAM = %s\t(true or false)", std::string(dateTime.isAM() ? "true":"false")));
    msg.Message(Poco::format("            isPM = %s\t(true or false)", std::string(dateTime.isPM() ? "true":"false")));
    msg.Message(Poco::format("      isLeapYear = %s\t(true or false)", std::string(Poco::DateTime::isLeapYear(dateTime.year()) ? "true":"false")));
    msg.Message(Poco::format("        hourAMPM = %d\t(0 to 12)", dateTime.hourAMPM()));
    msg.Message(Poco::format("       dayOfWeek = %d\t(0 to 6,   0: Sunday)", dateTime.dayOfWeek()));
    msg.Message(Poco::format("       dayOfYear = %d\t(1 to 366, 1: January 1)", dateTime.dayOfYear()));
    msg.Message(Poco::format("     daysOfMonth = %d\t(1 to 366, 1: January 1)", Poco::DateTime::daysOfMonth(dateTime.year(), dateTime.month())));
    msg.Message(Poco::format("            week = %d\t(0 to 53,  1: the week containing January 4)", dateTime.week()));

    msg.Message("");
}

int main(int /*argc*/, char** /*argv*/)
{
    PrepareConsoleLogger logger(Poco::Logger::ROOT, Poco::Message::PRIO_INFORMATION);

    ScopedLogMessage msg("DateTimeTest ", "start", "end");

    Poco::DateTime dateTime;

    msg.Message("  Current DateTime (UTC)");
    DisplayDateTime(dateTime, msg);

    msg.Message(Poco::format("  Current DateTime (Locat Time: %s [GMT%+d])", Poco::Timezone::name(), Poco::Timezone::tzd()/(60*60)));
    dateTime.makeLocal(Poco::Timezone::tzd());
    DisplayDateTime(dateTime, msg);

    msg.Message(Poco::format(Poco::DateTimeFormatter::format(dateTime, "  DateTimeFormatter: %w %b %e %H:%M:%S %%s %Y")
            ,	Poco::Timezone::name()));

    return 0;
}
```