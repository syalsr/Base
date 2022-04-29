# queue
![[../Files/Pasted image 20220425142927.png]]
FIFO - first in, first out. Основана на деке, работает быстрее,  но операций меньше. Это именно односторонняя очередь

```cpp
template <class T, class Container = deque<T> > class queue;
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

