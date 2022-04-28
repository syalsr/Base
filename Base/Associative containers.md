# Associative containers
В отличии от sequential контейнеров, асоциативные контейнеры не располагают свои элементы в линейной последовательности, представляют собой сбалансированное дерево и являются отображением T->U, в отличии от последовательных которых отображают индексы в T.

# map
Содержит в себе пары ключ - значение в отсортированном порядке по ключам.

```cpp
map<string, int> m {
	{ "Marc G.", 123 },
	{ "Warren B.", 456 },
	{ "Peter V.W.", 789 }
};
```

В отличии от вектора и листа, в order associative containers мы не вставляем элемент в определенную позицию поскольку они хранятся в отсортированном состоянии, элемент сам найдет куда встать.

## insert
Возвращает пару из итератора и bool - false если вставить не удалось.
```cpp
int main()
{
	map<int, int> m;
	m[0] = 0;
	m[1] = 1;
	auto [it, isinsert] = m.insert({ 0,5 });//т.к. ключ 0 уже есть, вставки не будет
	if(!isinsert)
		cout << "Fail insert";
	m[0] = 5;//тут уже меняем значение
	auto it = m.insert_or_assign( 1,3 );//если такой ключ есть, заменяем значение ключа, метод все равно вернет false
}
```

## operator[]
Возвращает ссылок на значение. С помощью данного оператора создаем ключ с значением по умолчанию, если нужно проверить значение или существование ключа лучше выбрать специальный метод.

```cpp
int main()
{
	map<int, int> m;
	m[0] = 0;
	m[0] = 1;//заменили значение
	auto it = m.find(1);//возвращает итератор на пару если такой ключ есть, либо нет если его нет
	if(it != m.end())
		cout << "Find it";
	if (m.count(0) == 1)//count возвращает количество ключей == 0, т.к. ключи уникальны, он всегда будет возвращать 1
		cout << "Count";	
}
```


## emplace
`emplace() и emplace_hint()` конструирует объект как и у вектора, `try_emplace()` вставляет элемент на место если ключ существует или ничего не делает

```cpp
int main()
{
	map<int, int> m;
	m[0] = 10;
	m.emplace(0,2);
	m.emplace_hint(prev(m.end()),0, 3);
	m.try_emplace(0, 1);
}
```

## erase
Удаляет пару по ключу

```cpp
int main()
{
	map<int, int> m;
	m[0] = 10;
	m.erase(0);
}
```

## Nodes
Т.к. мапа хранит элементы в дереве, у него есть узлы, родитель и 2 потомка. С C++17 можно вытащить эту ноду.

```cpp
int main()
{
	map<int, string> m{{1, "mango"}, {2, "papaya"}, {3, "guava"}};
	auto nh = m.extract(2);
	nh.key() = 4;
	m.insert(move(nh));
	// m == {{1, "mango"}, {3, "guava"}, {4, "papaya"}}
	map<int, int> src{ {1, 11}, {2, 22} };
	map<int, int> dst{ {2, 22}, {3, 33}, {4, 44}, {5, 55} };
	dst.merge(src);//merge мувает все ноды из одного в другой контейнер
	//в src осталось 2,22, поскольку случился конфликт
}
```

# Example
```cpp
class BankAccount final
{
public:
		BankAccount(int accountNumber, std::string name)
		: m_accountNumber{ accountNumber }, m_clientName{ std::move(name) }{}
	void setAccountNumber(int accountNumber) {
		m_accountNumber = accountNumber;
	}
	int getAccountNumber() const { return m_accountNumber; }
	void setClientName(std::string name) { m_clientName = std::move(name); }
	const std::string& getClientName() const { return m_clientName; }
private:
	int m_accountNumber;
	std::string m_clientName;
};
class BankDB final
{
public:
	// Adds account to the bank database. If an account exists already
	// with that number, the new account is not added. Returns true
	// if the account is added, false if it's not.
	bool addAccount(const BankAccount& account);
	// Removes the account with accountNumber from the database.
	void deleteAccount(int accountNumber);
	// Returns a reference to the account represented
	// by its number or the client name.
	// Throws out_of_range if the account is not found.
	BankAccount& findAccount(int accountNumber);
	BankAccount& findAccount(std::string_view name);
	// Adds all the accounts from db to this database.
	// Deletes all the accounts from db.
	void mergeDatabase(BankDB& db);
private:
	std::map<int, BankAccount> m_accounts;
};

bool BankDB::addAccount(const BankAccount& account)
{
	// Do the actual insert, using the account number as the key.
	auto res{ m_accounts.emplace(account.getAccountNumber(), account) };
	// or: auto res { m_accounts.insert(
	// pair { account.getAccountNumber(), account }) };
	// Return the bool field of the pair specifying success or failure.
	return res.second;
}

void BankDB::deleteAccount(int accountNumber)
{
	m_accounts.erase(accountNumber);
}

BankAccount& BankDB::findAccount(int accountNumber)
{
	// Finding an element via its key can be done with find().
	auto it{ m_accounts.find(accountNumber) };
	if (it == end(m_accounts)) {
		throw out_of_range{ "No account with that number." };
	}
	// Remember that iterators into maps refer to pairs of key/value.
	return it->second;
}

BankAccount& BankDB::findAccount(string_view name)
{
	// Finding an element by a non-key attribute requires a linear
	// search through the elements. With C++17 structured bindings:
	for (auto& [accountNumber, account] : m_accounts) {
		if (account.getClientName() == name) {
			return account; // found it!
		}
	}
	throw out_of_range{ "No account with that name." };
}

void BankDB::mergeDatabase(BankDB& db)
{
	// Use C++17 merge().
	m_accounts.merge(db.m_accounts);
	// Or: m_accounts.insert(begin(db.m_accounts), end(db.m_accounts));
	// Now clear the source database.
	db.m_accounts.clear();
}

int main()
{
	BankDB db;
	db.addAccount(BankAccount{ 100, "Nicholas Solter" });
	db.addAccount(BankAccount{ 200, "Scott Kleper" });
	try {
		auto& account{ db.findAccount(100) };
		cout << "Found account 100" << endl;
		account.setClientName("Nicholas A Solter");
		auto& account2{ db.findAccount("Scott Kleper") };
		cout << "Found account of Scott Kleper" << endl;
		auto& account3{ db.findAccount(1000) };
	}
	catch (const out_of_range& caughtException) {
		cout << "Unable to find account: " << caughtException.what() << endl;
	}
}
/*
	Found account 100
	Found account of Scott Kleper
	Unable to find account: No account with that number.
*/
```

# multimap
Контейнер позволяет хранить одинаковые ключи - из-за этого у контейнера нет `operator[] и at()`, метод find не обязательно будет ссылать на первый ключ, для поиска нужно использовать `lower_bound и upper_bound`, insert всегда успешен

```cpp
int main()
{
	multimap<int, int> m;
	auto it = m.insert({ 10, 10 });
	cout << it->first << " " << it->second;//10 10
	auto it1 = m.insert({ 1,3 });
}
```

## search
```cpp
int main()
{
	multimap<int, int> m;
	m.insert({ 1,1 });
	m.insert({ 1,2 });
	m.insert({ 1,3 });
	m.insert({ 1,4 });
	m.insert({ 1,5 });
	m.insert({ 2,4 });
	auto it = m.lower_bound(1);//указывает на первый элемент больший или равный данному
	auto it1 = m.upper_bound(1);//указывает на первый элемент больший данного
	auto it2 = m.equal_range(1);//пара итераторов от lower и upper bound
}
```

1. Если элемент есть, то `equal_range = [lower_bound, upper_bound)` – диапазон всех вхождений; 
2. Если же элемента нет, то `lower_bound == upper_bound` – позиция, куда можно вставить элемент без нарушения порядка сортировки;  
3. Количество вхождений `== upper_bound - lower_bound`;  
4. А перебрать все элементы, равные данному, можно просто проитерировавшись от `lower_bound` до `upper_bound`.

![[../Files/Pasted image 20220214173604.png]]

## Example
```cpp
class BuddyList final
{
public:
	// Adds buddy as a friend of name.
	void addBuddy(const std::string& name, const std::string& buddy);
	// Removes buddy as a friend of name.
	void removeBuddy(const std::string& name, const std::string& buddy);
	// Returns true if buddy is a friend of name, false otherwise.
	bool isBuddy(const std::string& name, const std::string& buddy) const;
	// Retrieves a list of all the friends of name.
	std::vector<std::string> getBuddies(const std::string& name) const;
private:
	std::multimap<std::string, std::string> m_buddies;
};

void BuddyList::addBuddy(const string& name, const string& buddy)
{
	// Make sure this buddy isn't already there. We don't want
	// to insert an identical copy of the key/value pair.
	if (!isBuddy(name, buddy)) {
		m_buddies.insert({ name, buddy }); // Using initializer_list
	}
}

void BuddyList::removeBuddy(const string& name, const string& buddy)
{
	// Obtain the beginning and end of the range of elements with
	// key 'name'. Use both lower_bound() and upper_bound() to demonstrate
	// their use. Otherwise, it's more efficient to call equal_range().
	auto begin{ m_buddies.lower_bound(name) }; // Start of the range
	auto end{ m_buddies.upper_bound(name) }; // End of the range
	// Iterate through the elements with key 'name' looking
	// for a value 'buddy'. If there are no elements with key 'name',
	// begin equals end, so the loop body doesn't execute.
	for (auto iter{ begin }; iter != end; ++iter) {
		if (iter->second == buddy) {
			// We found a match! Remove it from the map.
			m_buddies.erase(iter);
			break;
		}
	}
}

bool BuddyList::isBuddy(const string& name, const string& buddy) const
{
	// Obtain the beginning and end of the range of elements with
	// key 'name' using equal_range(), and C++17 structured bindings.
	auto [begin, end] { m_buddies.equal_range(name) };
	// Iterate through the elements with key 'name' looking
	// for a value 'buddy'.
	for (auto iter{ begin }; iter != end; ++iter) {
		if (iter->second == buddy) {
			// We found a match!
			return true;
		}
	}
	// No matches
	return false;
}

vector<string> BuddyList::getBuddies(const string& name) const
{
	// Obtain the beginning and end of the range of elements with
	// key 'name' using equal_range(), and C++17 structured bindings.
	auto [begin, end] { m_buddies.equal_range(name) };
	// Create a vector with all names in the range (all buddies of name).
	vector<string> buddies;
	for (auto iter{ begin }; iter != end; ++iter) {
		buddies.push_back(iter->second);
	}
	return buddies;
}

int main()
{
	BuddyList buddies;
	buddies.addBuddy("Harry Potter", "Ron Weasley");
	buddies.addBuddy("Harry Potter", "Hermione Granger");
	buddies.addBuddy("Harry Potter", "Hagrid");
	buddies.addBuddy("Harry Potter", "Draco Malfoy");
	// That's not right! Remove Draco.
	buddies.removeBuddy("Harry Potter", "Draco Malfoy");
	buddies.addBuddy("Hagrid", "Harry Potter");
	buddies.addBuddy("Hagrid", "Ron Weasley");
	buddies.addBuddy("Hagrid", "Hermione Granger");
	auto harrysFriends{ buddies.getBuddies("Harry Potter") };
	cout << "Harry's friends: " << endl;
	for (const auto& name : harrysFriends) {
		cout << "\t" << name << endl;
	}
}
/*
Harry's friends:
		Ron Weasley
		Hermione Granger
		Hagrid
*/
```

# set
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

# multiset
multiset это set который позволяет иметь дупликаты