# priority queue
Это queue хранящая элементы в отсортированном порядке, поддерживает [[Категории итераторов#Random access iterators]]. Головной элемент имеет наивысший приоритет.

```cpp
template <class T,  
        class Container = vector<T>,  
        class Compare = less<typename Container::value_type>>  
class priority_queue;

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

