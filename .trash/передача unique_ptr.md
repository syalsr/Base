# Способ 1. Принять объект по ссылке
```cpp
bool CheckUserPermissions(const UserInfo& user) {
	return user.access_level == AccessLevel::FULL;
}

// Можно вызвать от ссылки на объект из unique_ptr

unique_ptr<UserInfo> user_holder = /* ... */;

if (user_holder && !CheckUserPermissions(*user_holder)) {
	// ...
}
  
// Можно вызвать от ссылки на любой другой объект, даже временный
if (!CheckUserPermissions(UserInfo(/* ... */))) {
	// ...
}
```

1. **Что можно делать с параметром функции?** Всё то же, что и с любым другим константным объектом UserInfo. При этом знание о способе владения объектом потеряно
2. **Что можно передать в функцию?** Ссылку на любой объект UserInfo, не обязательно связанный с unique_ptr. При разыменовании умного указателя важно убедиться, что в нём лежит не nullptr.
3. **Когда использовать?** Всегда, когда для функции не имеет значения способ владения объектом.

# Способ 2. Принять объект по значению
```cpp
bool AccessManager::CheckUserPermissions(UserInfo user) const {
	const auto access_level = user.access_level;
	requests_history.push_back(move(user));
	return access_level == AccessLevel::FULL;
}
```

[[Передача параметра по значению]]

# Способ 3. Принять объект по обычному указателю
```cpp
bool CheckUserPermissions(const UserInfo* user) {
	return (user ? *user : GUEST).access_level == AccessLevel::FULL;
}

// Можно вызвать от указателя на объект из unique_ptr
unique_ptr<UserInfo> user_holder = /* ... */;

if (!CheckUserPermissions(user_holder.get())) {
	// ...
}

// Можно вызвать от ссылки на любой другой постоянный объект

UserInfo user = /* ... */;

if (!CheckUserPermissions(&user)) {
	// ...
}

// Можно вызвать от nullptr
if (!CheckUserPermissions(nullptr)) {
	// ...
} 

// Нельзя вызвать от временного объекта:
// CheckUserPermissions(&UserInfo(/* ... */));
  
// Можно вызвать от указателя,
// полученного из временного unique_ptr

if (!CheckUserPermissions(make_unique<UserInfo>(/* ... */).get())) {
	// ...
}
```

В этом случае объект может быть не передан, и этот случай нужно специальным образом обработать.

# Способ 4. Принять умный указатель по константной ссылке
```cpp
bool CheckUserPermissions(const unique_ptr<UserInfo>& user_holder) {
	return (user_holder ? *user_holder : GUEST).access_level == AccessLevel::FULL;
}

// Можно вызвать от существующего unique_ptr
unique_ptr<UserInfo> user_holder = /* ... */;
if (!CheckUserPermissions(user_holder)) {
	// ...
}

// Можно вызвать от временного unique_ptr
if (!CheckUserPermissions(make_unique<UserInfo>(/* ... */))) {
	// ...
}
```

1. **Что можно делать с параметром функции?** Вызывать любые константные методы unique_ptr. Но по сути единственный содержательный константный метод — это get(), поэтому с точки зрения самой функции этот способ ничем не отличается от способа 3 с константным указателем.
2. **Что можно передать в функцию?** Только ссылку на unique_ptr — существующий или вновь созданный. Способ 3 поддерживал эти сценарии, плюс дополнительные, не привязанные к unique_ptr.
3. **Когда использовать?** Никогда: способ 3 имеет более широкие возможности и более прозрачен.

# Способ 5. Принять умный указатель по значению
```cpp
class AccessManager {
public:
	void AddUser(unique_ptr<UserInfo> user_holder) {
		if (user_holder && user_holder->access_level != AccessLevel::NONE) {
			users_.push_back(move(user_holder));
		}
	}

private:
	vector<unique_ptr<UserInfo>> users_;
};

AccessManager manager;

// Можно вызвать от существующего unique_ptr,
// переместив его в функцию
unique_ptr<UserInfo> user_holder = /* ... */;
manager.AddUser(move(user_holder));

// Можно вызвать от временного unique_ptr

manager.AddUser(make_unique<UserInfo>(/* ... */));
```

1. **Что можно делать с параметром функции?** Использовать как полноценный unique_ptr: этот параметр владеет объектом.
2. **Что можно передать в функцию?** unique_ptr, который не нужен на вызывающей стороне: временный или перемещённый.
3. **Когда использовать?** В тех случаях, когда функция должна забрать владение объектом — например, для сохранения в контейнере.