# Golang

```go
s := `  
   {  "order_uid": "b563feb7b2b84b6test",  "track_number": "WBILMTESTTRACK",  "entry": "WBIL",  "delivery": {    "name": "Test Testov",    "phone": "+9720000000",    "zip": "2639809",    "city": "Kiryat Mozkin",    "address": "Ploshad Mira 15",    "region": "Kraiot",    "email": "test@gmail.com"  },  "payment": {    "transaction": "b563feb7b2b84b6test",    "request_id": "",    "currency": "USD",    "provider": "wbpay",    "amount": 1817,    "payment_dt": 1637907727,    "bank": "alpha",    "delivery_cost": 1500,    "goods_total": 317,    "custom_fee": 0  },  "items": [    {      "chrt_id": 9934930,      "track_number": "WBILMTESTTRACK",      "price": 453,      "rid": "ab4219087a764ae0btest",      "name": "Mascaras",      "sale": 30,      "size": "0",      "total_price": 317,      "nm_id": 2389212,      "brand": "Vivienne Sabo",      "status": 202    }  ],  "locale": "en",  "internal_signature": "",  "customer_id": "test",  "delivery_service": "meest",  "shardkey": "9",  "sm_id": 99,  "date_created": "2021-11-26T06:22:19Z",  "oof_shard": "1"}  
`  
   var orderInfo detail.OrderInfo  
   err := json.Unmarshal([]byte(s), &orderInfo)  
   if err != nil {  
      panic(err)  
   }  
  
   res, errr := json.Marshal(orderInfo)  
   if errr != nil {  
      panic(err)  
   }
```


JSON представляет собой одну из двух структур:
1.  набор пар ключ - значение;
2.  упорядоченный набор значений.
 
В качестве значений в JSON могут быть использованы:
1.  объект – это неупорядоченное множество пар ключ - значение, заключённое в фигурные скобки «{ }». Ключ описывается строкой, между ним и значением стоит символ «:». Пары ключ - значение отделяются друг от друга запятыми;
2.  массив — это упорядоченное множество значений, заключенных в квадратные скобки «[ ]». Значения разделяются запятыми;
3.  число (целое или вещественное);
4.  литералы true, false (логические) и null;
5.  строка.
```json
{
    "firstName": "Иван",
    "lastName": "Иванов",
    "address": {
        "streetAddress": "Московское ш., 101, кв.101",
        "city": "Ленинград",
        "postalCode": 101101
    },
    "phoneNumbers": [
        "812 123-1234",
        "916 123-4567"
    ]
}
```

```go
type myStruct struct {
	Name   string
	Age    int
	Status bool
	Values []int
}

s := myStruct{
	Name:   "John Connor",
	Age:    35,
	Status: true,
	Values: []int{15, 11, 37},
}

// Функция Marshal принимает аргумент типа interface{} (в нашем случае это структура)
// и возвращает байтовый срез с данными, кодированными в формат JSON.
data, err := json.Marshal(s)
if err != nil {
	fmt.Println(err)
	return
}

fmt.Printf("%s", data) // {"Name":"John Connor","Age":35,"Status":true,"Values":[15,11,37]}

data, err := json.MarshalIndent(s, "", "\t")  
if err != nil {  
fmt.Println(err)  
return  
}  
  
fmt.Printf("%s", data)  
  
//{  
// "Name": "John Connor",  
// "Age": 35,  
// "Status": true,  
// "Values": [  
//    15,  
//    11,  
//    37  
// ]  
//}
```

```json
data := []byte(`{"Name":"John Connor","Age":35,"Status":true,"Values":[15,11,37]}`)  
  
type myStruct struct {  
   Name   string  
   Age    int  
   Status bool  
   Values []int  
}  
  
var s myStruct  
  
if err := json.Unmarshal(data, &s); err != nil {  
fmt.Println(err)  
return  
}  
  
fmt.Printf("%v", s) // {John Connor 35 true [15 11 37]}
```

## Проверка json на правильность
Мы можем проверить является ли срез байтов форматом json:  
 ```go
type user struct {
	Name     string
	Email    string
	Status   bool
	Language []byte
}

m := user{Name: "John Connor", Email: "test email"}

data, _ := json.Marshal(m)

data = bytes.Trim(data, "{") // испортим json удалив '{'

// функция json.Valid возвращает bool, true - если json правильный
if !json.Valid(data) {
	fmt.Println("invalid json!") // вывод: invalid json!
}

fmt.Printf("%s", data) // вывод: "Name":"John Connor","Email":"test email","Status":false,"Language":null}
```

# Аннотирование структур
```go
// в общем виде аннотация выглядит так: `json:"используемое_имя,опция,опция"`

type myStruct struct {
	// при кодировании / декодировании будет использовано имя name, а не Name
	Name string `json:"name"`
	
	// при кодировании / декодировании будет использовано то же имя (Age),
	// но если значение поля равно 0 (пустое значение: false, nil, пустой слайс и пр.),
	// то при кодировании оно будет опущено
	Age int `json:",omitempty"`
	
	// при кодировании / декодировании поле всегда игнорируется
	Status bool `json:"-"`
}

m := myStruct{Name: "John Connor", Age: 0, Status: true}

data, err := json.Marshal(m)
if err != nil {
	fmt.Println(err)
	return
}

fmt.Printf("%s", data) // {"name":"John Connor"}
```

Неэкспортируемые поля (имена которых начинаются со строчной буквы) не участвуют в кодировании / декодировании.