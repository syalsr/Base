# Qt

# QJson
```cpp
[
    {
        "ds": "fs"
    },
    {
        "ds": "qs"
    }
]

QFile file;
file.setFileName("D:/Bank_account/js.json");
file.open(QIODevice::ReadOnly | QIODevice::Text);
QString val = file.readAll();
file.close();

QJsonDocument d = QJsonDocument::fromJson(val.toUtf8());
QJsonArray sett2 = d.array();

foreach(const QJsonValue& value, sett2) {
    QJsonObject obj = value.toObject();
    auto res = obj["ds"].toString();
    qInfo() << res;
}
/*
"fs"
"qs"
*/
```

```cpp
{
    "prop": [
		    {
		        "ds": "fs"
		    },
            {
                "ds": "qs"
            }
    ]
}

QJsonDocument d = QJsonDocument::fromJson(val.toUtf8());
auto sett2 = d.object();

foreach(const auto& value, sett2["prop"].toArray()) {
    QJsonObject obj = value.toObject();
    auto res = obj["ds"].toString();
    qInfo() << res;
}
/*
"fs"
"qs"
*/
```

```cpp
{
    "prop": [
		    {
	        "name": "Yar",
            "surname" : "Rar"
		    },
            {
                "name": "Zar",
                "surname" : "Dar"
            }
    ]
}

foreach(const auto& value, sett2["prop"].toArray()) {
    auto obj = value.toObject().toVariantMap();
    auto name = obj["name"].toString();
    auto surname = obj["surname"].toString();
    qInfo() << name << " " << surname;
}
```

```cpp
[
    "First thing",
    "Second thing",
    "Third thing"
]

QJsonArray json_array = json_doc.array();

for(i=0; i< json_array.count(); ++i){
    qDebug() << json_array.at(i).toString();
}
/*
"First thing"
"Second thing"
"Third thing"
*/
```

```cpp
{
    "stats": {
        "str": 34,
            "int" : 56,
            "con" : 22
    },
        "inventory" : [
            "sword",
                "shield",
                "armor"
        ]
}

QJsonDocument json_doc = QJsonDocument::fromJson(val.toUtf8());
auto root = json_doc.object();
auto rmap = root.toVariantMap();
auto stat = rmap["stats"].toMap();
auto inv = rmap["inventory"].toList();

foreach(auto val, stat)
{
    qInfo() << val.toString();
}
/*
    "22"
    "56"
    "34"
*/

qInfo() << stat["str"].toString() <<  stat["int"].toString() << stat["con"].toString();
/*"34" "56" "22"*/

foreach(auto val, inv)
{
    qInfo() << val.toString();
}
/*
    "sword"
    "shield"
    "armor"
*/
```