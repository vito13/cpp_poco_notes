# poco的任务模块
已经独立出来,其实就是多线程和一个具体应用,把线程处理任务的进度,能上报出来,具体分析如下:  
task主要应用在GUI和Seerver程序中，用于追踪后台线程的进度。
应用Poco任务时，需要类Poco::Task和类Poco::TaskManager配合使用。其中类Poco::Task继承自 Poco::Runnable，它提供了接口可以便利的报告线程进度。Poco::TaskManager则对Poco::Task进行管理。

为了完成取消和上报线程进度的工作：
1. 使用者必须从Poco::Task创建一个子类并重写runTask()函数
2. 为了完成进度上报的功能，在子类的runTask()函数中，必须周期的调用setProgress()函数去上报信息
3. 为了能够在任务运行时终止任务，必须在子类的runTask()函数中，周期性的调用isCancelled()或者sleep()函数，去检查是否有任务停止请求
4. 如果isCancelled()或者sleep()返回真，runTask()返回。

Poco::TaskManager通过使用Poco::NotificationCenter 去通知所有需要接受任务消息的对象，从上面描述可以看出，Poco中Task的功能就是能够自动汇报线程运行进度。调试案例如下
```
#include "Poco/Observer.h"
#include <iostream>
#include "Poco/Task.h"
#include "Poco/TaskManager.h"
#include "Poco/TaskNotification.h"
using Poco::Observer;
using namespace std;

class SampleTask: public Poco::Task
{
public:
    SampleTask(const std::string& name): Task(name)
    {}
    void runTask()
    {
        for (int i = 0; i < 100; ++i)
        {
            setProgress(float(i)/100); // report progress
            if (sleep(1000))
                break;
        }
    }
};

class ProgressHandler
{
public:
    void onProgress(Poco::TaskProgressNotification* pNf)
    {
     cout << pNf->task()->name()
            << " progress: " << pNf->progress() << endl;
     pNf->release();
    }
    void onFinished(Poco::TaskFinishedNotification* pNf)
    {
        cout << pNf->task()->name() << " finished." << std::endl;
        pNf->release();
    }
};

int main(int argc, char** argv)
{
    Poco::TaskManager tm;
    ProgressHandler pm;
    tm.addObserver(
        Observer<ProgressHandler, Poco::TaskProgressNotification>
        (pm, &ProgressHandler::onProgress)
        );
    tm.addObserver(
        Observer<ProgressHandler, Poco::TaskFinishedNotification>
        (pm, &ProgressHandler::onFinished)
        );
    tm.start(new SampleTask("Task 1")); // tm takes ownership
    tm.start(new SampleTask("Task 2"));
    tm.start(new SampleTask("Task3"));
    tm.joinAll();
    return 0;
}
```