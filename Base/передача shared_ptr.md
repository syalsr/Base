# Способы 1–3. Принять объект по ссылке, по значению или по обычному указателю
Здесь всё эквивалентно аналогичным способам для [[передача unique_ptr|unique_ptr]]: если функции нужно лишь прочитать данные из объекта, достаточно — в зависимости от ситуации — передать в неё ссылку, указатель или сам объект по значению.

# Способ 4. Принять умный указатель по константной ссылке
```cpp
bool CheckUserPermissions(const shared_ptr<UserInfo>& user) {
	return (user ? *user : GUEST).access_level == AccessLevel::FULL;
}

class AccessManager {
public:
	void AddUser(const shared_ptr<UserInfo>& user) {
		if (user && user->access_level != AccessLevel::NONE) {
			users_.push_back(user);
		}
	}

private:
	vector<shared_ptr<UserInfo>> users_;
};

// Можно вызвать от существующего shared_ptr
shared_ptr<UserInfo> user = /* ... */;
if (!CheckUserPermissions(user)) {
	// ...
} 

AccessManager manager;

// Можно вызвать от временного shared_ptr
manager.AddUser(make_shared<UserInfo>(/* ... */));
```
1. **Что можно делать с параметром функции?** Вызывать любые константные методы (но тогда это почти ничем не лучше сырого указателя) или с помощью копирования создавать новые shared_ptr, разделяющие владение объектом. Но во втором случае можно создать копию shared_ptr, приняв его по значению — см. способ 5.
2. **Что можно передать в функцию?** Ссылку на shared_ptr — существующий или вновь созданный.
3. **Когда использовать?** Никогда: см. способ 3 и способ 5.

# Способ 5. Принять умный указатель по значению
```cpp
class AccessManager {
public:
	void AddUser(shared_ptr<UserInfo> user) {
		if (user && user->access_level != AccessLevel::NONE) {
			users_.push_back(move(user));
		}
	}

private:
	vector<shared_ptr<UserInfo>> users_;
};

AccessManager manager; 

// Можно вызвать от существующего shared_ptr
shared_ptr<UserInfo> user = /* ... */;
manager.AddUser(user); 

// Можно вызвать от временного shared_ptr
manager.AddUser(make_shared<UserInfo>(/* ... */));
```
1. **Что можно делать с параметром функции?** Использовать как полноценный shared_ptr: этот параметр владеет объектом наравне с остальными shared_ptr.
2. **Что можно передать в функцию?** shared_ptr, постоянный (тогда он скопируется) или временный.
3. **Когда использовать?** В тех случаях, когда функция должна разделить владение объектом — например, для сохранения в контейнере.