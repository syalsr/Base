# mutex и lock_guard
mutex нужен чтобы защитить критическую секцию при гонке данных.

```cpp
struct Account {
	int balance = 0; 
	vector history; 
	bool Spend(int value) 
	{ 
		if (value <= balance) 
		{ 
			balance −= value; 
			history.push_back(value); 
			return true; 
		} 
		return false; 
	} 
};
```

Может случиться так, что несколько функций параллельно будут использовать функцию Spend(), и соответственно, может случиться так, что они используют старые значения, нам нужно защитить ее, сделать так, если в критической секции уже есть поток, то второй поток ждет, пока первый выйдет из нее. 

```cpp
struct Account {
	int balance = 0; 
	vector history;
	mutex m;
	bool Spend(int value) 
	{
		lock_guard<mutex> g(m);
		if (value <= balance) 
		{ 
			balance −= value; 
			history.push_back(value); 
			return true; 
		} 
		return false; 
	} 
};
```

Мьютекс может разлочить только тот поток, который заблокировал. [[semaphore]] же сам может заниматься этим

При использовании мьютекса может произойти взаимная блокировка - первый поток захватил ресурсы второго потока, а второй поток захватил ресурсы первого потока, каждый поток ждет пока каждый из них освободит ресурсы

# shared_mutex
Реализован в библиотеке буст, служит так называемым read/write mmutex, нужен когда у нас данные редко изменяются, но часто читаются. Работает он так:
1. У него есть две блокировки - разделяемая - для чтения и монопольная - для записи
2. Если какой-то поток захватил разделяемую блокировку, то если второй поток хочет захватить монопольную, он будет ждать, пока все потоки захватившие разделяемую блокировку не освободят его

```cpp
#include <map>
#include <string>
#include <mutex>
#include <boost/thread/shared_mutex.hpp>
class dns_entry;
class dns_cache
{
    std::map<std::string, dns_entry> entries;
    mutable boost::shared_mutex entry_mutex;
public:
    dns_entry find_entry(std::string const& domain) const
    {
        boost::shared_lock<boost::shared_mutex> lk(entry_mutex);
        std::map<std::string, dns_entry>::const_iterator const it =
            entries.find(domain);
        return (it == entries.end()) ? dns_entry() : it->second;
    }
    void update_or_add_entry(std::string const& domain,
        dns_entry const& dns_details)
    {
        std::lock_guard<boost::shared_mutex> lk(entry_mutex);
        entries[domain] = dns_details;
    }
};
```

# recuresive_mutex
Нужен для того, чтобы повторно захватывать мьютекс, не освобождая его, но есть важная особенность, сколько раз мы его захватили, столько раз должны свободить, т.е. 3 раза `lock()` $=>$ 3 раза `unlock()`, но при применении `unique_lock()` это должно происходить автоматически.