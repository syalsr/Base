# 20 EME
# Работа с таблицами
```cpp
Constructor:
Table = Object("Table",,"Table");
TableT = Object("dsDB", "TableTest");

Phone = Object("Control", , "Phone");
Family = Object("Control", , "Family");

Methods:
Table_OnInput()
{
    famLine = Table.GetCurrentRow();
    If (~TableT.IsValidPhoneNumber(Phone.GetFromRow(famLine)))
        Phone.PutText(" ");
        is_message("Внимание", "Invalid phone number", 1, 1);
    End If
}
Table_OnBeforeSave()//перед сохранением проходим по всем строкам таблицы и заменяется телефоны
{
    For (row = 0; row < Table.GetNoOfLines(); row = row + 1)
        val = "test" + row;
        Phone.PutToRow(val, row);
    End For
}
Table_OnBeforeSave()//перед сохранением проходим по всем строкам таблицы и заменяется телефоны
{
    For (row = 0; row < Table.GetNoOfLines(); row = row + 1)
        val = Phone.GetFromRow(row) + " - Phone number";
        Phone.PutToRow(val, row);
    End For
}
Family_OnInput()
{
    If (Family.GetText() == "123")//если значения органа редактор текста 123, то менеяем на text
        Family.PutText("text");
    End If
}
```

# Дебаг сборка
Заходим в настройки дебаг версии
Команда: `C:\metaproj\kernel.x64\Exe\debug\db.exe`
Аргументы команды(Берем из нашего ярлыка mono): `/mono "C:\metaproj\dat_ws1\eme.hdr" /http /d_emel /nodllalign`
Рабочий каталог`(C:\metaproj\metaproj\exe\Debug`): `..\..\exe\Debug`
Запускаем через Visual Studio, откроется EME и далее делаем, что нужно. Не забываем поставить брейкпоинт