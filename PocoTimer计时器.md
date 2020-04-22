# 定时器
做到现在，感觉poco的应用场景（见本博客的其它博文）还是很好的，在这些应用场景下，构架中的类直接封装到位，方便应用开发。
面向对象的目的在于软件代码的重复利用，避免重复造轮子，提高开发效率。POCO很好的体现了这个原则，和Qt一样完善，可以说已经面向组件的开发。
下面开始看看本章节的内容：

## poco c++定时器本质分析  
定时器作为线程的扩展，也是编程时经常会被用到的元素。
在程序设计上，定时器的作用是很简单。预定某个定时器，即希望在未来的某个时刻，程序能够得到时间到达的触发信号。
本定时器的应用场景很明确了：在线程中定时执行任务，如定时发送请求报文，定时备份数据。
编程时，一般对定时器使用有下面一些关注点：
1. 定时器的精度。Poco中的定时器精度并不是很高，具体精度依赖于实现的平台(Windows or Linux)
2. 定时器是否可重复，即定时器是否可触发多次。 Poco中的定时器精度支持多次触发也支持一次触发，由其构造函数Timer决定
3. 一个定时器是否可以设置多个时间。 Poco中定时器不支持设置多个时间，每个定时器对应一个时间。如果需要多个时间约定的话，使用者要构造多个定时器。

## poco c++定时器的应用步骤
### 定义定时器变量  
```
class MovieMode
{
    Poco::Timer m_timerRestartLive;
};
```
### 声明并实现回调函数
```
void OnTimerReStart(Poco::Timer& timer);
void MovieMode::OnTimerReStart(Poco::Timer& timer);
{
}
```
### 设置时间间隔
```
m_timerRestartLive.setStartInterval(10000);     //第一次，多长时间启动
m_timerRestartLive.setPeriodicInterval(1000); //设置定时时间间隔
可以这样写的： Timer timer(2500, 500);
```
### 注册回调函数
```
Poco::TimerCallback<MovieMode> timerCallback(*this, &MovieMode::OnTimerReStart);
```
### 开启定时器
```
m_timerRestartLive.start(timerCallback, *BigThreadPool::GetInstance());
```
### 退出定时器
```
m_timerRestartLive.stop(); 
```
## 程序例子
程序的涵义：程序启动2.5秒后，开启定时器，在余下的2.5秒内，每隔0.5秒，执行一次。
```
#include "Poco/Timer.h"
#include "Poco/Thread.h"
#include "Poco/Stopwatch.h"
#include <iostream>
using Poco::Timer;
using Poco::TimerCallback;
using Poco::Thread;
using Poco::Stopwatch;

class TimerExample
{
public:
    TimerExample()
    {
        _sw.start();
    }
    void onTimer(Timer& timer)
    {
        std::cout << "Callback called after " << _sw.elapsed()/1000 << " milliseconds." << std::endl;
    }
private:
    Stopwatch _sw;
};

int main(int argc, char** argv)
{
    TimerExample example;
    Timer timer(2500, 500);
    timer.start(TimerCallback<TimerExample>(example, &TimerExample::onTimer));
    Thread::sleep(5000);
   // timer.stop();
    return 0;
}
```