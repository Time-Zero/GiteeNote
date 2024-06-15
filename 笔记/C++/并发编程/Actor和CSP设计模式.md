# Actor模式
简单的说，Actor模式的基本原理就是建立一个队列，多个线程向队列中传递数据，而只有一个线程从队列中读出数据

这样线程和线程之间、类和类直接不直接交互，而是通过消息队列传递消息，降低的代码的耦合度并且某一线程或者某一类崩溃之后不直接影响其他功能的运行

# CSP模式
CSP模式是Communicating Sequential Process的简称，中文可以叫做顺序通信进程，也是一种并发模型

CSP和Actor类似，不过CSP将消息投递给channel，至于谁从channel中取数据，发送方是不关注的。相较于Actor模式，Actor模式在发送消息前是知道接收方是谁，而接收方收到消息后也知道发送方是谁的，更像邮件的通信模式。而CSP确是完全解耦合的 

下面是一个简单的CSP实现：
```C++
#include <chrono>
#include <cstddef>
#include <iostream>
#include <mutex>
#include <condition_variable>
#include <queue>
#include <thread>
  
template <typename T>
class Channel{
private:
    std::queue<T> queue_;
    std::mutex mtx_;
    std::condition_variable cv_producer_;
    std::condition_variable cv_consumer_;
    size_t capactiy_;
    bool closed_ = false;
  
public:
    Channel(size_t capacity = 0) : capactiy_(capacity){}        // 队列容量为0， 也就是这是一个无缓冲区的队列

    bool send(T value){
        std::unique_lock<std::mutex> lock(mtx_);
        cv_producer_.wait(lock, [this](){           // 对于生产者来说，如果是无缓冲区的队列，并且队列为空，则直接放入元素
            return (capactiy_ == 0 && queue_.empty() || queue_.size() < capactiy_ || closed_);
        });

  
        if(closed_)
            return false;
  
        queue_.push(value);
        cv_consumer_.notify_one();
 
		return true;
    }

  
    bool receive(T& value){
        std::unique_lock<std::mutex> lock(mtx_);
        cv_consumer_.wait(lock,[this](){        // 对于消费者，如果其中有数据那么我肯定可以取或者被要求关闭了
            return !queue_.empty() || closed_;
        });

  
        if(closed_ && queue_.empty())
            return false;

        value = queue_.front();
        queue_.pop();
        cv_producer_.notify_one();

        return true;

    }

  

    void close(){
        std::unique_lock<std::mutex> lock(mtx_);
        closed_ = true;
        cv_consumer_.notify_all();
        cv_producer_.notify_all();
    }

};

  

int main(){

    Channel<int> ch(0);

    std::thread producer([&](){
        for(int i = 0; i < 5; i++){
            ch.send(i);
            std::cout << "Sent: " << i << std::endl;
        }

        ch.close();
    });

  

    std::thread consumer([&](){
        std::this_thread::sleep_for(std::chrono::milliseconds(500));
        int val;
        while(ch.receive(val)){
            std::cout << "Receive: " << val << std::endl;
        }
    });

  

    producer.join();
    consumer.join();

    return 0;
}
```