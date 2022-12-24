# std::stack
![[../Files/Pasted image 20220425142905.png]]
LIFO - last in, first out. Позволяет лишь добавлять в конец и удалять из конца, основан на деке.

```cpp
template <class T, class Container = deque<T> > class stack;
int main()
{
    stack<int> q;
    q.push(1);
    q.emplace(2);
    q.pop();
    auto t = q.top();
}
```

