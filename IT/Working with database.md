# Работа Golang с базой данных
```yml
docker-compose.yml

version: "3"

services:
	image: postgres
	tty: true
	restart: always
	ports:
		- "5432:5432"
	environment:
		- POSTGRES_PASSWORD=postgres
		- POSTGRES_DB=test_database
```