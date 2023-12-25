# C++: producer-comsumer model

```cpp
#include <bits/stdc++.h>
#include <string>

std::queue<int> q;
std::mutex mtx;
std::condition_variable cv;

void produce(int i) {
    {
        std::unique_lock<std::mutex> lck(mtx);
        q.push(i);
    }
    cv.notify_one();
}

void producer() {
    for (int i = 0; i < 100; i ++) {
        produce(i);
    }
    produce(-1);
}

void consumer(int id) {
    while (true) {
        int t;
        {
            std::unique_lock<std::mutex> lck(mtx);
            cv.wait(lck, []() {  return !q.empty();  });
            t = q.front();
            if (t == -1) {
                break;
            }
            q.pop();
        } 
        std::cout << std::string::format("Consumed {} by {}", t, id) << std::endl;
    }
}

int main() {
    std::thread producer_thread(producer);
    std::vector<std::thread> consumer_threads;
    for (int i = 0; i < 32; i++) {
        std::thread consumer_thread(consumer, i);
        consumer_threads.push_back(std::move(consumer_thread));
    }
    for (int i = 0; i < 32; i++) {
        consumer_threads[i].join();
    }
    producer_thread.join();
    return 0;
}
```
