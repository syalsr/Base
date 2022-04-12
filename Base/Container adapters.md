# Container adapters
# queue
Основана на деке, работает быстрее,  но операций меньше. Это именно односторонняя очередь

```cpp
q.push(x), q.pop() // вставляем в начало и удаляем из конца 
q.front(), q.back() // ссылки на первый и последний элементы очереди 
q.size(), q.empty() // размер и проверка на пустоту
```

```cpp
template <typename T>
class PacketBuffer
{
public:
    // If maxSize is 0, the size is unlimited, because creating
    // a buffer of size 0 makes little sense. Otherwise only
    // maxSize packets are allowed in the buffer at any one time.
    explicit PacketBuffer(size_t maxSize = 0);
    virtual ~PacketBuffer() = default;
    // Stores a packet in the buffer.
    // Returns false if the packet has been discarded because
    // there is no more space in the buffer, true otherwise.
    bool bufferPacket(const T& packet);
    // Returns the next packet. Throws out_of_range
    // if the buffer is empty.
    [[nodiscard]] T getNextPacket();
private:
    std::queue<T> m_packets;
    size_t m_maxSize;
};
template <typename T> PacketBuffer<T>::PacketBuffer(size_t maxSize/*= 0*/)
    : m_maxSize{ maxSize }
{
}
template <typename T> bool PacketBuffer<T>::bufferPacket(const T& packet)
{
    if (m_maxSize > 0 && m_packets.size() == m_maxSize) {
        // No more space. Drop the packet.
        return false;
    }
    m_packets.push(packet);
    return true;
}

template <typename T> T PacketBuffer<T>::getNextPacket()
{
    if (m_packets.empty()) {
        throw std::out_of_range{ "Buffer is empty" };
    }
    // Retrieve the head element
    T temp{ m_packets.front() };
    // Pop the head element
    m_packets.pop();
    // Return the head element
    return temp;
}

class IPPacket final
{
public:
    explicit IPPacket(int id) : m_id{ id } {}
    int getID() const { return m_id; }
private:
    int m_id;
};
int main()
{
    PacketBuffer<IPPacket> ipPackets{ 3 };
    // Add 4 packets
    for (int i{ 1 }; i <= 4; ++i) {
        if (!ipPackets.bufferPacket(IPPacket{ i })) {
            cout << "Packet " << i << " dropped (queue is full)." << endl;
        }
    }
    while (true) {
        try {
            IPPacket packet{ ipPackets.getNextPacket() };
            cout << "Processing packet " << packet.getID() << endl;
        }
        catch (const out_of_range&) {
            cout << "Queue is empty." << endl;
            break;
        }
    }
}
/*
    Packet 4 dropped (queue is full).
    Processing packet 1
    Processing packet 2
    Processing packet 3
    Queue is empty.
*/
```

# priority queue
Это queue хранящая элементы в отсортированном порядке, поддерживает [[Категории итераторов#Random access iterators]]. Головной элемент имеет наивысший приоритет.

```cpp
int main()
{
    priority_queue<int> q;
    q.push(1);
    q.emplace(2);
    q.pop();
    auto t = q.top();//получаем const ref
}
```

```cpp
class Error final
{
public:
    Error(int priority, std::string errorString)
        : m_priority{ priority }, m_errorString{ std::move(errorString) } { }
    int getPriority() const { return m_priority; }
    const std::string& getErrorString() const { return m_errorString; }
    // Compare Errors according to their priority. (C++20 operator<=>)
    auto operator<=>(const Error& rhs) const {
        return getPriority() <=> rhs.getPriority();
    }
private:
    int m_priority;
    std::string m_errorString;
};
// Stream insertion overload for Errors.
std::ostream& operator<<(std::ostream& os, const Error& err)
{
    os << std::format("{} (priority {})", err.getErrorString(), err.getPriority());
    return os;
}
// Simple ErrorCorrelator class that returns highest priority errors first.
class ErrorCorrelator final
{
public:
    // Add an error to be correlated.
    void addError(const Error& error) { m_errors.push(error); }
    // Retrieve the next error to be processed.
    [[nodiscard]] Error getError()
    {
        // If there are no more errors, throw an exception.
        if (m_errors.empty()) {
            throw std::out_of_range{ "No more errors." };
        }
        // Save the top element.
        Error top{ m_errors.top() };
        // Remove the top element.
        m_errors.pop();
        // Return the saved element.
        return top;
    }
private:
    std::priority_queue<Error> m_errors;
};
int main()
{
    ErrorCorrelator ec;
    ec.addError(Error{ 3, "Unable to read file" });
    ec.addError(Error{ 1, "Incorrect entry from user" });
    ec.addError(Error{ 10, "Unable to allocate memory!" });
    while (true) {
        try {
            Error e{ ec.getError() };
            cout << e << endl;
        }
        catch (const out_of_range&) {
            cout << "Finished processing errors" << endl;
            break;
        }
    }
}
/*
    Unable to allocate memory! (priority 10)
    Unable to read file (priority 3)
    Incorrect entry from user (priority 1)
    Finished processing errors
*/
```

# Stack
Позволяет лишь добавлять в конец и удалять из конца. Как queue, но обеспечивает семантику FILO - первый вошел, последний вышел

```cpp
int main()
{
    stack<int> q;
    q.push(1);
    q.emplace(2);
    q.pop();
    auto t = q.top();
}
```