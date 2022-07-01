# Dijkstra's algorithm
Еще один алгоритм на [[Graph#Взвешенный граф|взвешенном графе]] который учитывает вес ребер. Он работает только с **направленными ациклическими графами - Directed Acyclic Graph - DAG**

![[Drawing 2022-05-07 10.37.21.excalidraw]]

В чем суть алгоритма:
![[Drawing 2022-05-07 10.19.22.excalidraw]]
Ищем кратчайший путь из C в D, т.к. мы не знаем сколько нам потребуется минут для достижения цели будем считать это за бесконечность минут.
1. Найти ребро с наименьшей стоимостью - CB
2. Вычислить сколько времени потребуется для того, чтобы добраться до всех соседей B при переходе по ребру из B:
	1. До A  5 минут - короче чем из C сразу в A
	2. До D 7 минут - уменьшили время до D с бесконечности до 7 минут
3. Повторить, вычисляем путь из С в A, он оказывается больше, значение не обновляем, далее идем из A в D и сравниваем. Получилось, что самый кратчайший путь занимает 6 минут.

![[../Excalidraw/dijkstra.gif]]

```cpp
struct Edge
{
	size_t weight;
	size_t to;
};

struct Vertice
{
	size_t id;
	std::vector<Edge> edges;
	size_t weight;
	size_t from;
	bool visited;
};

struct Graph
{
	std::vector<Vertice> vertices;
	void clear()
	{
		vertices.clear();
	}
	void Init()
	{
		for (auto&& node : vertices)
		{
			node.weight = std::numeric_limits<size_t>::max();
			node.visited = false;
			node.from = std::numeric_limits<size_t>::max();
		}
	}
};

void dijkstra(Graph& graph, size_t start)
{
	std::multimap<size_t, size_t> weights;
	weights.insert({ start, 0 });
	graph.vertices[start].weight = 0;

	while (!weights.empty())
	{
		auto [first_id, first_weight] = *(weights.begin());
		weights.erase(weights.begin());
		if (graph.vertices[first_id].visited)
			continue;
		graph.vertices[first_id].visited = true;

		for (size_t i{}; i < graph.vertices[first_id].edges.size(); ++i)
		{
			size_t to = graph.vertices[first_id].edges[i].to;
			size_t weight = graph.vertices[first_id].edges[i].weight;
			if (!graph.vertices[to].visited)
			{
				if (first_weight + weight < graph.vertices[to].weight)
				{
					graph.vertices[to].weight = first_weight + weight;
					graph.vertices[to].from = first_id;
					weights.insert({ to, graph.vertices[to].weight });
				}
			}
		}
	}

}

std::vector<size_t> create_path(const Graph& graph, size_t start, size_t finish)
{
	std::vector<size_t> path;
	path.push_back(graph.vertices[finish].id);
	size_t index = finish;
	while (index != start)
	{
		path.push_back(graph.vertices[index].from);
		index = graph.vertices[index].from;
	}
	std::reverse(path.begin(), path.end());
	return path;
}

void print_path(std::vector<size_t> path)
{
	for (auto&& node : path)
	{
		if (!(node == 3))
			std::cout << node << "->";
		else
			std::cout << node;

	}
}

void test_dijkstra()
{
	Vertice zero{ 0,
	{
			 {7, 1},
			 {9, 2},
			 {14, 5}
			}
	};
	Vertice one{ 1,
		{
				 {10, 2},
				 {15, 3}
				}
	};
	Vertice two{ 2,
		{
				 {2, 5},
				 {11, 3}
				}
	};
	Vertice three{ 3 };
	Vertice four{ 4,
		{{6, 3}}
	};
	Vertice five{ 5,
		{{9, 4}}
	};
	Graph graph{ {zero,one, two, three, four, five} };
	graph.Init();

	dijkstra(graph, 0);
	auto path = create_path(graph, 0, 3);
	print_path(path);
}
```

1. Ищем узел с наименьшей стоимостью
2. Получаем стоимость и соседей этого узла
3. Перебираем соседей и считаем суммарный путь до них
4. Сравниваем со старой стоимостью перехода
5. Обновляем значение, если надо


Алгоритм не может использовать при отрицательных весах, в задаче выше отрицательных весов может не быть, но такие задачи есть:

![[Drawing 2022-05-07 10.56.54.excalidraw]]

Это граф обменов с доплатой, у вас есть книга, вам нужен барабан
1. Вы можете обменять книгу на постер потратив 0 рублей, потом постер на барабан с доплатой в 35 рублей
2. А можете обменять книгу на пластинку за 5 рублей, а потом пластинку на постер и вам заплатят за это еще 7 рублей, а потом обменяете на барабан. Суммарно выйдет 33 рубля.

Алгоритм Дейскстры не применим, потому что к постеру самый дешевый путь в 0 рублей, а если мы пойдем сначала до пластинки это уже 5 рублей, алгоритм не учитывает, что стоимость может снизиться. Для поиска кратчайшего пути с отрицательными весами существует [[Алгоритм Беллмана - Форда]]