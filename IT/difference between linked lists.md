# Разница между list и forward list
1 - контейнер поддерживает операцию, 0 - контейнер не поддерживает операцию


| OPERATION                               | list | forward_list |
| --------------------------------------- | ---- | ------------ |
| assign()                                | 1    | 1            |
| back()                                  | 1    | 0            |
| before_begin()                          | 0    | 1            |
| begin()                                 | 1    | 1            |
| cbefore_begin()                         | 0    | 1            |
| cbegin()                                | 1    | 1            |
| cend()                                  | 1    | 1            |
| clear()                                 | 1    | 1            |
| crbegin()                               | 1    | 0            |
| crend()                                 | 1    | 0            |
| emplace()                               | 1    | 0            |
| emplace_after()                         | 0    | 1            |
| emplace_back()                          | 1    | 0            |
| emplace_front()                         | 1    | 1            |
| empty()                                 | 1    | 1            |
| end()                                   | 1    | 1            |
| erase()                                 | 1    | 0            |
| erase_after()                           | 0    | 1            |
| front()                                 | 1    | 1            |
| insert()                                | 1    | 0            |
| insert_after()                          | 0    | 1            |
| iterator / const_iterator               | 1    | 1            |
| max_size()                              | 1    | 1            |
| merge()                                 | 1    | 1            |
| pop_back()                              | 1    | 0            |
| pop_front()                             | 1    | 1            |
| push_back()                             | 1    | 0            |
| push_front()                            | 1    | 1            |
| rbegin()                                | 1    | 0            |
| remove()                                | 1    | 1            |
| remove_if()                             | 1    | 1            |
| rend()                                  | 1    | 0            |
| resize()                                | 1    | 1            |
| reverse()                               | 1    | 1            |
| reverse_iterator/const_reverse_iterator | 1    | 0            |
| size()                                  | 1    | 0            |
| sort()                                  | 1    | 1            |
| splice()                                | 1    | 0            |
| splice_after()                          | 0    | 1            |
| swap()                                  | 1    | 1            |
| unique()                                | 1    | 1            |