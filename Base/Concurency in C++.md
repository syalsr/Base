# Concurency
* [[async и future]]
* [[mutex и lock_guard]]
* [[semaphore]]
* [[condition variable]]
* [[Константность в многопоточных программах]]
* [[mutable в многопоточных программах]]
* [[Ошибки в многопоточных программах]]

```cpp
#include <iostream>
#include <thread>
void hello()
{
	std::cout<<"Hello Concurrent World\n";
}
int main()
{
	std::thread t(hello);//теперь наша программа работает в 2 потока
	t.join();//присоедиянием 2(hello()) поток к 1(int main()), поскольку иначе, если это не сделать и 1 поток закончит свою
	//работу раньше чем 2 поток, то программа завершится и 2 поток полностью не отработает
}
```
# Временный объект и поток
```cpp
class background_task
{
public:
	void operator()() const
	{
		do_something();
		do_something_else();
		}
};
background_task f;
std::thread my_thread(f);//ok
std::thread my_thread(background_task());//это не запуск поток а объявление функции, есть 2 варианта решения проблемы
std::thread my_thread((background_task()));
std::thread my_thread{background_task()};
```

# Лямбда и поток
```cpp
std::thread my_thread([](
	do_something();
	do_something_else();
});
```

# Проблемы с работой с удаленным ресурсом
```cpp
struct func
{
	int& i;
	func(int& i_):i(i_){}
	void operator()()
	{
		for(unsigned j=0;j<1000000;++j)
		{
			do_something(i);
		}
	}
};
void oops()
{
	int some_local_state=0;
	func my_func(some_local_state);
	std::thread my_thread(my_func);
	my_thread.detach();//не ждем завершения потока, т.е. функцию oops может отработать, а поток еще нет
	//если так случится, то поток будет взаимодействовать с удаленным объектов mu_func() - UB
}
```

Для решения этой проблемы нужно присоединиться к нему т.е. вместо `my_thread.detach()` написать `my_thread.join()`.

Повторно присоединиться к потоку нельзя, поскольку функция `join()` очищает любую память связанную с данным потоком

Вызывать функции 

# Потоки и исключения
Может случится так, что вылетит исключения до того, как мы присоединим поток.

```cpp
struct func
{
	int& i;
	func(int& i_):i(i_){}
	void operator()()
	{
		for(unsigned j=0;j<1000000;++j)
		{
			do_something(i);
		}
	}
};
void f()
{
	int some_local_state=0;
	func my_func(some_local_state);
	std::thread t(my_func);
	try
	{
		do_something_in_current_thread();
	}
	catch(...)
	{
		t.join();//сначала завершится поток t, потом произойдет выход из функции
		throw;
	}
	t.join();
}
```

Можно использовать RAII, такой же прием используется в [[mutex и lock_guard|lock_guard]]

```cpp
class thread_guard
{
	std::thread& t;
public:
	explicit thread_guard(std::thread& t_) :
		t(t_)
	{}
	~thread_guard()
	{
		if (t.joinable())
		{
			t.join();
		}
	}
	thread_guard(thread_guard const&) = delete;
	thread_guard& operator=(thread_guard const&) = delete;
};
struct func;
void f()
{
	int some_local_state = 0;
	func my_func(some_local_state);
	std::thread t(my_func);
	thread_guard g(t);
	do_something_in_current_thread();
}
```

# detach
Вызов этой функции позволяет потоку работать в фоновом режиме отсоединившись от основного потока. Как и в случае с `join()` `detach()` можно вызывать лишь один раз, и лишь если метод `joinable()` возвращает `true`. 

# Передача аргументов функции в поток
Параметры переданные в поток копируются, даже если аргумент имеет тип T&
```cpp
void f(Data& d);
std::thread t(f,3, data);
std::thread t(f,3, std::ref(data));//так копируются
```

# Вызов указателя на метод
```cpp
class X
{
public:
	void do_lengthy_work(int i);
};
X my_x;
std::thread t(&X::do_lengthy_work, &my_x, 4);//будет вызыван my_x.do_lenghtly_work()
```

# unique_ptr и thread
```cpp
void process_big_object(std::unique_ptr<big_object>);
std::unique_ptr<big_object> p(new big_object);
p->prepare_data(42);
std::thread t(process_big_object,std::move(p));//сначала ресурсы перемещают потоку, поток функции
```

# Передача владения потоком
```cpp
void some_function();
void some_other_function();
std::thread t1(some_function);
std::thread t2=std::move(t1);
t1=std::thread(some_other_function);
std::thread t3;
t3=std::move(t2);
t1=std::move(t3);//ошибка, т.к. t1 уже имеет поток
```

```cpp
void f(std::thread t);
void some_function();
void g()
{
	f(std::thread(some_function));
	std::thread t(some_function);
	f(std::move(t));
}
```

```cpp
class scoped_thread  
{  
    std::thread t;  
public:  
    explicit scoped_thread(std::thread t_):  
            t(std::move(t_))  
    {  
        if(!t.joinable())  
            throw std::logic_error(“No thread”);  
    }  
    ~scoped_thread()  
    {  
        t.join();  
    }  
    scoped_thread(scoped_thread const&)=delete;  
    scoped_thread& operator=(scoped_thread const&)=delete;  
};  
struct func;  
void f()  
{  
    int some_local_state;  
    scoped_thread t(std::thread(func(some_local_state)));  
    do_something_in_current_thread();  
}
```

```cpp
void do_work(unsigned id);
void f()
{
//Запуск нескольких потоков и ожидание их завершения
	std::vector<std::thread> threads;
	for(unsigned i=0;i<20;++i)
	{
		threads.push_back(std::thread(do_work,i));
	}
	std::for_each(threads.begin(),threads.end(),
	std::mem_fn(&std::thread::join));
}
```

# Выбор количества потоков во время выполнения
Для этой задачи существует функция `std::thread::hardware_concurrency()` которая возвращает количество потоков которые могут работать параллельно, которая равна количеству ядер.

# unique_lock
С помощью данного класса, мы можем выбирать в какой момент временить залочить мьютекс, но из-за этого работает медленнее, чем `lock_guard`, зато это может повысить производительность кода.

```cpp
mutex m;
void test()
{
	unique_lock<mutex> mm{m, defer_lock};//defer_lock означат, что в момент конструировать мьютекс не должен захватываться
	...
	mm.lock();
	...
	mm.unlock();
	...
	mm.lock();
	...
	mm.unlock();
	...
}
```

# Защита разделяемых данных во время инициализации
```cpp
std::shared_ptr<some_resource> resource_ptr;
std::once_flag resource_flag;
void init_resource()
{
	resource_ptr.reset(new some_resource);
}
void foo()
{
	std::call_once(resource_flag,init_resource);//Инициализация производится ровно один раз
	resource_ptr->do_something();
}
```

```cpp
class X
{
private:
	connection_info connection_details;
	connection_handle connection;
	std::once_flag connection_init_flag;
	void open_connection()
	{
		connection = connection_manager.open(connection_details);
	}
public:
	X(connection_info const& connection_details_) :
		connection_details(connection_details_)
	{}
	void send_data(data_packet const& data) 􀁙 􀁮
	{
		std::call_once(connection_init_flag, &X::open_connection, this);
		connection.send_data(data);
	}
	data_packet receive_data() 􀁙 􀁰
	{
		std::call_once(connection_init_flag, &X::open_connection, this);
		return connection.receive_data();
	}
};
```
