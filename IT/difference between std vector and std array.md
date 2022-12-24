# Deque vs Vector
## Заполнение
```cpp
static void DequeFill(benchmark::State& state) {  
    for (auto _ : state) {  
        std::deque<int> d;  
        for (int i = 0; i < 100000; ++i)  
            d.push_back(i);  
        benchmark::DoNotOptimize(d);  
    }  
}  
BENCHMARK(DequeFill);  
  
static void VectorFill(benchmark::State& state) {  
    for (auto _ : state) {  
        std::vector<int> v;  
        for (int i = 0; i < 100000; ++i)  
            v.push_back(i);  
        benchmark::DoNotOptimize(v);  
    }  
}  
BENCHMARK(VectorFill);
```

![[../Files/Pasted image 20220425140318.png]]
[Quick bench](https://quick-bench.com/q/gzYuHHieOrubE5kJ5PavQy_u6s4)
## Вставка
```cpp
static std::random_device rd;  
static std::mt19937 rng{rd()};  
  
int dice(int min, int max) {  
    std::uniform_int_distribution<int> uid(min, max);  
    return uid(rng);  
}  
  
constexpr int size = 1000;  
  
static void DequeAcc(benchmark::State& state) {  
    std::deque<int> d;  
    for (int i = 0; i < size; ++i)  
        d.push_back(i);  
    for (auto _ : state) {  
        int j = dice(0, size - 1);  
        j = d[j];  
        benchmark::DoNotOptimize(j);  
    }  
}  
BENCHMARK(DequeAcc);  
  
static void VectorAcc(benchmark::State& state) {  
    std::vector<int> v;  
    for (int i = 0; i < size; ++i)  
        v.push_back(i);  
    for (auto _ : state) {  
        int j = dice(0, size - 1);  
        j = v[j];  
        benchmark::DoNotOptimize(j);  
    }  
}  
BENCHMARK(VectorAcc);
```

![[../Files/Pasted image 20220425140752.png]]
[Quick bench](https://quick-bench.com/q/RuJH7bh8isdJ5lXamRQEODCrLNw)

Из бенчмарков понятно, что вставка в дек в начало и конец эффективнее, чем у вектора, но из середины нет.