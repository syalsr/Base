# std::set
В этом контейнере значением является ключ, хранит значения в отсортированном порядке без дупликатов.

## example
```cpp
class AccessList final
{
public:
	// Default constructor
	AccessList() = default;
	// Constructor to support uniform initialization.
	AccessList(std::initializer_list<std::string_view> users)
	{
		m_allowed.insert(begin(users), end(users));
	}
	// Adds the user to the permissions list.
	void addUser(std::string user)
	{
		m_allowed.emplace(std::move(user));
	}
	// Removes the user from the permissions list.
	void removeUser(const std::string& user)
	{
		m_allowed.erase(user);
	}
	// Returns true if the user is in the permissions list.
	bool isAllowed(const std::string& user) const
	{
		return (m_allowed.count(user) != 0);
	}
	// Returns a vector of all the users who have permissions.
	std::vector<std::string> getAllUsers() const
	{
		return { begin(m_allowed), end(m_allowed) };
	}
private:
	std::set<std::string> m_allowed;
};

int main()
{
	AccessList fileX{ "mgregoire", "baduser" };
	fileX.addUser("pvw");
	fileX.removeUser("baduser");
	if (fileX.isAllowed("mgregoire")) {
		cout << "mgregoire has permissions" << endl;
	}
	if (fileX.isAllowed("baduser")) {
		cout << "baduser has permissions" << endl;
	}
	auto users{ fileX.getAllUsers() };
	for (const auto& user : users) {
		cout << user << " ";
	}
}
/*
	mgregoire has permissions
	mgregoire pvw
*/
```

## merge && extract
```cpp
struct NCString : public string { 
	using string::string; 
	NCString(const NCString&) = delete; 
	NCString(NCString&&) = default; 
};
int main(){
	set<NCString> ss; 
	ss.insert("Aaa"); 
	ss.insert("Bbb"); 
	ss.insert("Ccc"); 
	for (const auto& el : ss) {
		cout << el << ’ ’; 
	} 
	cout << endl; 
	auto it = ss.begin();
	auto node = ss.extract(it); //перемещаем целый узел
	string& temp = node.value();
	temp[0] = tolower(temp[0]); 
	ss.insert(move(node));
}
```

```cpp
struct NCString : public string { 
	using string::string; 
	NCString(const NCString&) = delete; 
	NCString(NCString&&) = default; 
};
int main(){
	set<NCString> ss1;
	ss1.insert("Aaa"); 
	ss1.insert("Bbb"); 
	ss1.insert("Ccc"); 
	
	set<NCString> ss2; 
	ss2.insert("Xxx"); 
	ss2.insert("Yyy"); 
	ss2.insert("Zzz");
	//хотим слить два дерева, можно с помощью цикла и extract вставлять каждый узел, а можно одной функцией merge
	ss1.merge(ss2);
}
```

# std::multiset
multiset это set который позволяет иметь дупликаты