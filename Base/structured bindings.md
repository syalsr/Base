---
aliases:
tags: C++17
---

1. tuple - объекты могут быть разных типов, с c++17 можно сразу распаковать в переменных
```cpp
tuple<TaskInfo, TaskInfo> TeamTasks::PerfomPersonTasks(...) { 
	... 
	return {move(updated_tasks), move(untouched_tasks)}; 
}
auto [updated_tasks, untouched_tasks] = tasks.PerformPersonTasks(...);
tie(updated_tasks, untouched_tasks) = tasks.PerformPersonTasks(...);//придетяс явно определять переменные
```

Но в данном примере удобнее поместить TaskInfo в структуру, т.к. не понятно чем отличаются 2 TaskInfo.

```cpp
struct PerformResult { 
	TaskInfo updated_tasks; 
	TaskInfo untoucned_tasks; 
};
PerformResult TeamTasks::PerfomPersonTasks(...) { 
	... 
	return {move(updated_tasks), move(untouched_tasks)}; 
}
```

Если при возврате одной переменной из функций вас спасал NRVO, то когда вы возвращаете набор переменных, объединяя их в фигурные скобки, чтобы у вас получилась пара или кортеж, никакого NRVO не будет, и вам надо явно вызвать move от этих переменных при передаче в конструктор пары или кортежа.