# Dijkstra's algorithm
Еще один алгоритм на [[Graph#Взвешенный граф|взвешенном графе]] который учитывает вес ребер. Он работает только с **направленными ациклическими графами - Directed Acyclic Graph - DAG**

![[Drawing 2022-05-07 10.37.21.excalidraw]]

В чем суть алгоритма:
![[Drawing 2022-05-07 10.19.22.excalidraw]]
Ищем кратчайший путь из C в D, т.к. мы не знаем сколько нам потребуется минут для достижения цели будем считать это за бесконечность минут.
1. Найти узел с наименьшей стоимостью. Узел B ближайший
2. Вычислить сколько времени потребуется для того, чтобы добраться до всех соседей B при переходе по ребру из B:
	1. До A 3 минуты(начиная с C 5 минут) - короче чем из C сразу в A
	2. До D 5 минут(начиная с C 7 минут) - уменьшили время до D с бесконечности до 7 минут
3. Повторить, вычисляем путь из С в A, он оказывается больше, значение не обновляем, далее идем из A в D и сравниваем. Получилось, что самый кратчайший путь занимает 6 минут.

![[../Excalidraw/dijkstra.gif]]

```cpp
const auto INF = std::numeric_limits<int>::max();  
  
struct Edge  
{  
    int weight;  
    std::size_t indexTo;  
};  
  
struct Node  
{  
    int id;  
    std::vector<Edge> edges;  
  
    std::size_t prevIndex;  
    int weight;  
    bool visited;  
};  
  
struct Graph  
{  
    std::vector<Node> nodes;  
  
    void clear()  
    {  
        nodes.clear();  
    }  
  
    void clear_edges()  
    {  
        for (auto& node : nodes)  
        {  
            node.edges.clear();  
        }  
    }  
  
    void init_start_values()  
    {  
        for (auto& node : nodes)  
        {  
            node.weight = INF;  
            node.visited = false;  
            node.prevIndex = INF;  
        }  
    }  
  
};  
  
bool read_nodes(std::istream& istream, std::size_t nodes_count, Graph& graph_out)  
{  
    graph_out.nodes.clear();  
  
    for (std::size_t i = 0; i < nodes_count; i++)  
    {  
        decltype(Node::id) id;  
        istream >> id;  
        graph_out.nodes.push_back({ id });  
    }  
  
    return true;  
}  
  
bool read_edges(std::istream& istream, std::size_t edges_count, Graph& graph_out)  
{  
    graph_out.clear_edges();  
  
    for (std::size_t i = 0; i < edges_count; i++)  
    {  
        int start_id, end_id;  
        int weight;  
  
        istream >> start_id >> end_id;  
        istream >> weight;  
  
        auto& nodes_ref = graph_out.nodes;  
  
        auto start_iter = std::find_if(nodes_ref.begin(), nodes_ref.end(), [start_id](const auto& node) { return node.id == start_id; });  
        auto end_iter = std::find_if(nodes_ref.begin(), nodes_ref.end(), [end_id](const auto& node) { return node.id == end_id; });  
  
        if (start_iter == nodes_ref.end() || end_iter == nodes_ref.end())  
        {  
            graph_out.clear_edges();  
  
            return false;  
        }  
        std::size_t index = (end_iter - nodes_ref.begin());  
        (*start_iter).edges.push_back(Edge{ weight, index });  
    }  
  
    return true;  
}  
  
Graph read_graph(const std::string& file_path)  
{  
    Graph graph;  
    std::ifstream fin(file_path);  
    if (fin)  
    {  
        std::size_t node_count;  
        int edge_count;  
  
        fin >> node_count >> edge_count;  
  
        if (read_nodes(fin, node_count, graph))  
        {  
            read_edges(fin, edge_count, graph);  
        }  
    }  
    return graph;  
}  
  
std::vector<std::size_t> convert_graph_to_path(Graph& graph, std::size_t start_index, std::size_t end_index)  
{  
    std::vector<std::size_t> result;  
    std::stack<std::size_t> tmp_path;  
    std::size_t current_node = end_index;  
    while (current_node != INF)  
    {  
        tmp_path.push(current_node);  
        current_node = graph.nodes[current_node].prevIndex;  
    }  
    while (!tmp_path.empty())  
    {  
        result.push_back(tmp_path.top());  
        tmp_path.pop();  
    }  
    return result;  
}  
  
std::vector<std::size_t> find_path_Dijkstra(Graph& graph, std::size_t start_index, std::size_t end_index)  
{  
    graph.init_start_values();  
    std::multimap<int, std::size_t> min_weigth_map;  
    graph.nodes[start_index].weight = 0;  
    min_weigth_map.insert({ 0, start_index });  
    while (!min_weigth_map.empty())  
    {  
        auto [current_weight, current_index] = *(min_weigth_map.begin());  
        min_weigth_map.erase(min_weigth_map.begin());  
        if (graph.nodes[current_index].visited)  
        {  
            continue;  
        }  
  
        graph.nodes[current_index].visited = true;  
  
        for (std::size_t i = 0; i < graph.nodes[current_index].edges.size(); i++)  
        {  
            std::size_t index_to = graph.nodes[current_index].edges[i].indexTo;  
            int edged_weight = graph.nodes[current_index].edges[i].weight;  
            if (!graph.nodes[index_to].visited)  
            {  
                if (current_weight + edged_weight < graph.nodes[index_to].weight )  
                {  
                    graph.nodes[index_to].weight = current_weight + edged_weight;  
                    graph.nodes[index_to].prevIndex = current_index;  
                    min_weigth_map.insert({ graph.nodes[index_to].weight, index_to });  
                }  
            }  
        }  
  
    }  
  
    return convert_graph_to_path(graph, start_index, end_index);  
}  
  
int main()  
{  
    auto graph = read_graph("input.txt");  
    auto path = find_path_Dijkstra(graph, 0, 4);  
  
    for (auto path_node_index : path)  
    {  
        std::cout << graph.nodes[path_node_index].id << " ";  
    }  
  
    std::cout << std::endl;  
}
```

1. Ищем узел с наименьшей стоимостью
2. Получаем стоимость и соседей этого узла
3. Перебираем соседей и суммарный путь C-B-A
4. Сравниваем со старой стоимостью перехода C-A
5. Обновляем значение, если надо


Алгоритм не может использовать при отрицательных весах, в задаче выше отрицательных весов может не быть, но такие задачи есть:

![[Drawing 2022-05-07 10.56.54.excalidraw]]

Это граф обменов с доплатой, у вас есть книга, вам нужен барабан
1. Вы можете обменять книгу на постер потратив 0 рублей, потом постер на барабан с доплатой в 35 рублей
2. А можете обменять книгу на пластинку за 5 рублей, а потом пластинку на постер и вам заплатят за это еще 7 рублей, а потом обменяете на барабан. Суммарно выйдет 33 рубля.

Алгоритм Дейскстры не применим, потому что к постеру самый дешевый путь в 0 рублей, а если мы пойдем сначала до пластинки это уже 5 рублей, алгоритм не учитывает, что стоимость может снизиться. Для поиска кратчайшего пути с отрицательными весами существует [[Алгоритм Беллмана - Форда]]