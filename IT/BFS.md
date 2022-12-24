# BFS - breadth-first search - поиск в ширину
Алгоритм для поиска кратчайшего пути в [[Graph]], ищет всевозможные пути из A в Б и решает какой из них короче. Поскольку он перебирает все  возможные варианты, тогда он начинает с предположения: "Можно ли добраться до города за 1 ход" и проверяет соединен ли город  A с точкой C, оказывается нет, тогда она проходит по все возможным городам с которым соединена сам(B и D) и проверяет не соединены ли они с городом C и т.д. 

![[../Excalidraw/Drawing 2022-05-06 17.51.03.excalidraw]]

Получается он проверяется сначала уровень 1, потом уровень 2 и т.д.

![[Drawing 2022-05-06 18.04.01.excalidraw]]

Как работает алгоритм? Создать очередь из потенциальных кандидатов первого уровня, начать проверять, если первый не подошел, то добавляем всех его соседей в конец очереди  и продолжаем.

![[../Excalidraw/BFS.gif]]
Сложность в худшем случае $O(V+E)$, если мы проходим по всем ребрам и добавляем все вершины.

```cpp
bool is_seller(const std::string& name) {
    return name.back() == 'm';
}

template <typename T>
bool search(const T& name, const std::unordered_map<T, std::vector<T>>& graph) {
    std::queue<T> search_queue;
    std::unordered_set<T> searched;

    // add all friends to search queue
    for (auto&& friend_name : graph.find(name)->second)
        search_queue.push(friend_name);

    while (!search_queue.empty()) {
        T& person = search_queue.front();
        search_queue.pop();

        // only search this person if you haven't already searched them.
        if (searched.find(person) == searched.end()) {
            if (is_seller(person)) {
                cout << person << " is a mango seller!" << endl;
                return true;
            }
            
            // add all friends of a person to search queue
            for (auto&& friend_name : graph.find(person)->second)
                search_queue.push(friend_name);

            // mark this person as searched
            searched.insert(person);
        }
    }

    return false;
}

int main() {
    std::unordered_map<std::string, std::vector<std::string>> graph;
    graph.insert({"you", {"alice", "bob", "claire"}});
    graph.insert({"bob", {"anuj", "peggy"}});
    graph.insert({"alice", {"peggy"}});
    graph.insert({"claire", {"thom", "jonny"}});
    graph.insert({"anuj", {}});
    graph.insert({"peggy", {}});
    graph.insert({"thom", {}});
    graph.insert({"jonny", {}});

    std::string name = "you";
    bool result = search(name, graph);
    cout << "Found mango seller: " << result << endl;
}
```