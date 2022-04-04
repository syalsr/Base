## const static переменные
```cpp
class Spreadsheet
{
public:
	Spreadsheet(size_t width = MaxWidth, size_t height = MaxHeight)
		: m_width { min(width, MaxWidth) }
		, m_height { min(height, MaxHeight) }
	{
	}
	static const size_t MaxHeight { 100 };//Чтобы всякий раз не выделять память
	static const size_t MaxWidth { 100 };//делаем максимальные значение длины и высоты static const
};
```