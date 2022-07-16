# 10 Databases
Существуют реляционные базы данных, что подразумевает relation, отношение. Также их называют - табличные БД. 

База данных - совокупность различных данных, систематизированных определенных образом.
СУБД - система управления базами данных - ПО которое управляет созданием, редактированием и пользованием баз данных
# Полезное
1. https://15445.courses.cs.cmu.edu/fall2019/schedule.html
2. https://15445.courses.cs.cmu.edu/fall2020/project0/
3. [CMU 15-721 Advance Database](https://www.youtube.com/playlist?list=PLSE8ODhjZXjasmrEd2_Yi1deeE360zv5O)
4. [Awesome CMU Database](https://db.cs.cmu.edu/courses/)
5. SQL Tutorial
	1. [1](https://sqlzoo.net/wiki/SQL_Tutorial)
	2. [2](https://selectstarsql.com)
	3. [3](https://mystery.knightlab.com)
	4. [4](https://sqlbolt.com/lesson/select_queries_introduction)

# SQL - язык для взаимодействия с реляционными БД
`SELECT *  FROM Name_table `- вся таблица
`SELECT Id  FROM Name_table`  - колонка Id
`SELECT Id, Title  FROM Name_table`  - колонки Id, Title
```sql
SELECT Title  FROM Movies
WHERE Id = 6
Выведет название 6 фильма
```

```sql
SELECT column, another_column, … FROM mytable 
WHERE _condition_ AND/OR _another_condition_ AND/OR …;
```
|IN (…)|Number exists in a list | col_name IN (2, 4, 6)|

| Operator            | Condition                                            | SQL Example                   |
| ------------------- | ---------------------------------------------------- | ----------------------------- |
| =, !=, < <=, >, >=  | Standard numerical operators                         | col_name != 4                 |
| BETWEEN … AND …     | Number is within range of two values (inclusive)     | col_name BETWEEN 1.5 AND 10.5 |
| NOT BETWEEN … AND … | Number is not within range of two values (inclusive) | col_name NOT BETWEEN 1 AND 10 |
| IN (…)              | Number exists in a list                              | col_name IN (2, 4, 6)         |
| NOT IN (…)          | Number does not exist in a list                      | col_name NOT IN (1, 3, 5)     |

| Operator   | Condition                                                                                             | Example                                                              |
| ---------- | ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| =          | Case sensitive exact string comparison (_notice the single equals_)                                   | col_name = "abc"                                                     |
| != or <>   | Case sensitive exact string inequality comparison                                                     | col_name != "abcd"                                                   |
| LIKE       | Case insensitive exact string comparison                                                              | col_name LIKE "ABC"                                                  |
| NOT LIKE   | Case insensitive exact string inequality comparison                                                   | col_name NOT LIKE "ABCD"                                             |
| %          | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) | col_name LIKE "%AT%"   (matches "AT", "ATTIC", "CAT" or even "BATS") |
| $_$        | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE)                    | col_name LIKE "AN_"  (matches "AND", but not "AN")                   |
| IN (…)     | String exists in a list                                                                               | col_name IN ("A", "B", "C")                                          |
| NOT IN (…) | String does not exist in a list                                                                       | col_name NOT IN ("D", "E", "F")                                      |