# condition variable
Эта переменная ассоциирована с каким-то событием или иным условием, остальные потоки ждут пока оно выполнится, если некоторый поток решит, что условие выполнено, он может известить об этом один или несколько потоков, ожидающих условную переменную, в результате чего они возобновят работу.

В стандартной библиотеке для этого есть `std::condition_varialbe` и `std::condition_variable_any`. Для обеспечения синхронизации необходимо взаимодействие с мьютексом; первый класс может
работать только с std::mutex, второй – с любым классом, который отвечает минимальным требованиям мьютекса.

```cpp
std::mutex mut;
std::queue<data_chunk> data_queue;
std::condition_variable data_cond;
void data_preparation_thread()
{
    while (more_data_to_prepare())
    {
        data_chunk const data = prepare_data();
        std::lock_guard<std::mutex> lk(mut);
        data_queue.push(data);
        data_cond.notify_one();//извещаем один ожидающий поток, если он есть
    }
}
void data_processing_thread()
{
    while (true)
    {
        std::unique_lock<std::mutex> lk(mut);
        data_cond.wait(
            lk, [] {return !data_queue.empty(); });//передаем мьютекс и ожидающее условие, 
            //wait() вызывает лямбду  и передает управление обратно, если условие выполнилось, 
            //если же условие false, то wait() освобождает мьютекс и переводит поток в состояние ожидания
            //когда конвар получит извещение, поток опять захватит мьютекс и проверит условие
            //если условие истинно, то поток передаст управление дальше с захваченным мьютексом
            //поэтому мы используем unique_lock, чтобы освобождать мьютекс, иначе бы он не освободился
            //и data_preparation на захватил бы его
        data_chunk data = data_queue.front();
        data_queue.pop();
        lk.unlock();
        process(data);
        if (is_last_chunk(data))
            break;
    }
}
```

# thread-safe queue с condition variables
```cpp
template <class T, class Container = std::deque<T> >
class queue {
public:
    explicit queue(const Container&);
    explicit queue(Container && = Container());
    template <class Alloc> explicit queue(const Alloc&);
    template <class Alloc> queue(const Container&, const Alloc&);
    template <class Alloc> queue(Container&&, const Alloc&);
    template <class Alloc> queue(queue&&, const Alloc&);
    void swap(queue& q);
    bool empty() const;
    size_type size() const;
    T& front();
    const T& front() const;
    T& back();
    const T& back() const;
    void push(const T& x);
    void push(T&& x);
    void pop();
    template <class... Args> void emplace(Args&&... args);
};

template<typename T>
class threadsafe_queue
{
private:
    mutable std::mutex mut;
    std::queue<T> data_queue;
    std::condition_variable data_cond;
public:
    threadsafe_queue()
    {}
    threadsafe_queue(threadsafe_queue const& other)
    {
        std::lock_guard<std::mutex> lk(other.mut);
        data_queue = other.data_queue;
    }
    void push(T new_value)
    {
        std::lock_guard<std::mutex> lk(mut);
        data_queue.push(new_value);
        data_cond.notify_one();
    }
    void wait_and_pop(T& value)
    {
        std::unique_lock<std::mutex> lk(mut);
        data_cond.wait(lk, [this] {return !data_queue.empty(); });
        value = data_queue.front();
        data_queue.pop();
    }
    std::shared_ptr<T> wait_and_pop()
    {
        std::unique_lock<std::mutex> lk(mut);
        data_cond.wait(lk, [this] {return !data_queue.empty(); });
        std::shared_ptr<T> res(std::make_shared<T>(data_queue.front()));
        data_queue.pop();
        return res;
    }
    bool try_pop(T& value)
    {
        std::lock_guard<std::mutex> lk(mut);
        if (data_queue.empty())
            return false;
        value = data_queue.front();
        data_queue.pop();
        return true;
    }
    std::shared_ptr<T> try_pop()
    {
        std::lock_guard<std::mutex> lk(mut);
        if (data_queue.empty())
            return std::shared_ptr<T>();
        std::shared_ptr<T> res(std::make_shared<T>(data_queue.front()));
        data_queue.pop();
        return res;
    }
    bool empty() const
    {
        std::lock_guard<std::mutex> lk(mut);
        return data_queue.empty();
    }
};
```