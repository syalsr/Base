# Associative containers
# set && map
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

Тоже самое работает и со словарем