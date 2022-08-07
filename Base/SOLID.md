# SOLID
## **S**RP – single responcibility principle 
Каждый контекст должен иметь одну ответственность. Пример плохо проектирования SRP:

![[../Files/Pasted image 20220424114315.png]]

```cpp
using Screen = std::ostream;  
using ByteStream = std::ostream;  
  
struct Vector3D {  
    int x, y, z;  
    Vector3D &operator+=(const Vector3D &lhs) {  
        x += lhs.x;  
        y += lhs.y;  
        z += lhs.z;  
        return *this;  
    }  
};  
  
struct Quaternion {  
    Vector3D v;  
    int w;  
};  
  
void draw(Screen &s, Vector3D v) {  
    s << "(" << v.x << ", " << v.y << ", " << v.z << ")";  
}  
  
class Polygon3D {  
    std::vector<Vector3D> vs_;  
  
public:  
    Polygon3D(std::initializer_list<Vector3D> il) : vs_(il) {}  
    void translate(const Vector3D &t) {  
        for (auto &v : vs_)  
            v += t;  
    }  
    void rotate(const Quaternion &q) {  
        // for (auto &p : vs_)  
        //   p = inverse(q) * p * q;    }  
    void draw(Screen &s) const {  
        for (auto v : vs_) {  
            ::draw(s, v);  
            std::cout << "\n";  
        }  
    }  
    void serialize(ByteStream &bs) const { draw(bs); }  
};  
  
int main() {  
    Polygon3D p = {{2, 1, 6}, {-3, 7, 4}};  
    p.draw(std::cout);  
    //(2, 1, 6)  
    //(-3, 7, 4)}
```

Что не так? Класс должен иметь лишь одну причину, чтобы поменяться, в нашем же случае:
1. Что-то изменилось в vector3d
2. Что-то изменилось в Screen, 
3. Что-то изменилось в ByteStream

Решение - написать функции вместо методов для рисования и сериализации, а для доступа к данным сделаем [[итераторы]] по нашим данным.

![[../Files/Pasted image 20220424114349.png]]

Проявляет принцип единственной ответственности, теперь полигону не нужно дополнительно выводить свои данные, он не обязан это мочь.

```cpp
using Screen = std::ostream;  
using ByteStream = std::ostream;  
  
struct Vector3D {  
    int x, y, z;  
    Vector3D &operator+=(const Vector3D &lhs) {  
        x += lhs.x;  
        y += lhs.y;  
        z += lhs.z;  
        return *this;  
    }  
};  
  
struct Quaternion {  
    Vector3D v;  
    int w;  
};  
  
void draw(Screen &s, Vector3D v) {  
    s << "(" << v.x << ", " << v.y << ", " << v.z << ")";  
}  
  
class Polygon3D {  
    std::vector<Vector3D> vs_;  
    using CItt = typename std::vector<Vector3D>::const_iterator;  
  
public:  
    Polygon3D(std::initializer_list<Vector3D> il) : vs_(il) {}  
    void translate(const Vector3D &t) {  
        for (auto &v : vs_)  
            v += t;  
    }  
    void rotate(const Quaternion &q) {  
        // for (auto &p : vs_)  
        //   p = inverse(q) * p * q;    }  
  
    CItt begin() const { return vs_.cbegin(); }  
    CItt end() const { return vs_.cend(); }  
};  
  
void draw(Screen &s, const Polygon3D &p) {  
    for (auto v : p) {  
        ::draw(s, v);  
        std::cout << "\n";  
    }  
}  
  
void serialize(ByteStream &bs, const Polygon3D &p) { draw(bs, p); }  
  
int main() {  
    Polygon3D p = {{2, 1, 6}, {-3, 7, 4}};  
    draw(std::cout, p);  
    //(2, 1, 6)  
    //(-3, 7, 4)}
```

## **O**CP – open-close principle
Каждый контекст должен быть закрыт для изменения и открыт для расширения

![[../Files/Pasted image 20220424114209.png]]
```cpp
struct IFigure {  
    enum class Shape { VECTOR, POLYGON, CIRCLE };  
    virtual Shape shape() const = 0;  
    virtual ~IFigure() = default;  
};  
  
struct IScreen {  
    virtual void draw(const IFigure &f) = 0;  
    virtual void render() const = 0;  
    virtual ~IScreen() = default;  
};  
  
using ByteStream = IScreen;  
void serialize(ByteStream &bs, const IFigure &p) { bs.draw(p); }  
  
struct Vector3D : public IFigure {  
    int x_, y_, z_;  
    Vector3D(int x = 0, int y = 0, int z = 0) : x_(x), y_(y), z_(z) {}  
    Vector3D &operator+=(const Vector3D &lhs) {  
        x_ += lhs.x_;  
        y_ += lhs.y_;  
        z_ += lhs.z_;  
        return *this;  
    }  
  
    Shape shape() const override { return IFigure::Shape::VECTOR; }  
};  
  
struct Quaternion {  
    Vector3D v;  
    int w;  
};  
  
class Polygon3D : public IFigure {  
    std::vector<Vector3D> vs_;  
    using CItt = typename std::vector<Vector3D>::const_iterator;  
  
public:  
    Polygon3D(std::initializer_list<Vector3D> il) : vs_(il) {}  
    void translate(const Vector3D &t) {  
        for (auto &v : vs_)  
            v += t;  
    }  
    void rotate(const Quaternion &q) {  
        // for (auto &p : vs_)  
        //   p = inverse(q) * p * q;    }  
    Shape shape() const override { return IFigure::Shape::POLYGON; }  
  
    CItt begin() const { return vs_.cbegin(); }  
    CItt end() const { return vs_.cend(); }  
};  
  
class Screen : public IScreen {  
    std::vector<const IFigure *> figures_;  
  
    void drawVector(const Vector3D &v) const {  
        std::cout << "(" << v.x_ << ", " << v.y_ << ", " << v.z_ << ")";  
    }  
  
    void drawPolygon(const Polygon3D &p) const {  
        for (auto v : p) {  
            drawVector(v);  
            std::cout << "\n";  
        }  
    }  
  
public:  
    void draw(const IFigure &f) override { figures_.push_back(&f); }  
  
    void render() const override {  
        for (auto f : figures_) {  
            switch (f->shape()) {  
                case IFigure::Shape::POLYGON:  
                    drawPolygon(*static_cast<const Polygon3D *>(f));  
                    break;  
                case IFigure::Shape::VECTOR:  
                    drawVector(*static_cast<const Vector3D *>(f));  
                    break;  
            }  
        }  
    }  
};  
  
int main() {  
    Polygon3D p = {{2, 1, 6}, {-3, 7, 4}};  
    Screen s;  
    s.draw(p);  
    s.render();  
    //(2, 1, 6)  
    //(-3, 7, 4)}
```

Почему плохо? Добавили еще один Shape Circle, теперь мы не можем расширить класс Screen(к слову название не удачное, правильнее View_model), мы можем его только модифицировать, внести изменение в метод `render()`. Когда мы расширяем класс мы не меняем интерфейс и его код, мы что-то дописываем рядом.

Есть 1 вариант решения, но он нарушает наш первый принцип, наши фигуры должны уметь себя рисовать:

![[../Files/Pasted image 20220424114242.png]]
```cpp
struct IDrawable;  
  
struct IScreen {  
    virtual void render() const = 0;  
    virtual void draw(std::shared_ptr<IDrawable> f) = 0;  
    std::ostream &stream() const { return std::cout; }  
    virtual ~IScreen() = default;  
};  
  
struct IDrawable {  
    virtual void draw(const IScreen &s) const = 0;  
    virtual ~IDrawable() = default;  
};  
  
using ByteStream = IScreen;  
void serialize(ByteStream &bs, std::shared_ptr<IDrawable> p) { bs.draw(p); }  
  
struct Vector3D : public IDrawable {  
    int x_, y_, z_;  
    Vector3D(int x = 0, int y = 0, int z = 0) : x_(x), y_(y), z_(z) {}  
    Vector3D &operator+=(const Vector3D &lhs) {  
        x_ += lhs.x_;  
        y_ += lhs.y_;  
        z_ += lhs.z_;  
        return *this;  
    }  
  
    void draw(const IScreen &s) const override {  
        s.stream() << "(" << x_ << ", " << y_ << ", " << z_ << ")";  
    }  
};  
  
struct Quaternion {  
    Vector3D v;  
    int w;  
};  
  
class Polygon3D : public IDrawable {  
    std::vector<Vector3D> vs_;  
    using CItt = typename std::vector<Vector3D>::const_iterator;  
  
public:  
    Polygon3D(std::initializer_list<Vector3D> il = {}) : vs_(il) {}  
    void translate(const Vector3D &t) {  
        for (auto &v : vs_)  
            v += t;  
    }  
    void rotate(const Quaternion &q) {  
        // for (auto &p : vs_)  
        //   p = inverse(q) * p * q;    }  
    void draw(const IScreen &s) const override {  
        for (auto v : vs_) {  
            v.draw(s);  
            s.stream() << "\n";  
        }  
    }  
  
    CItt begin() const { return vs_.cbegin(); }  
    CItt end() const { return vs_.cend(); }  
};  
  
class Screen : public IScreen {  
    std::vector<std::shared_ptr<IDrawable>> figures_;  
  
public:  
    void draw(std::shared_ptr<IDrawable> f) override { figures_.push_back(f); }  
  
    void render() const override {  
        for (auto f : figures_)  
            f->draw(*this);  
    }  
};  
  
int main() {  
    std::initializer_list<Vector3D> il{{2, 1, 6}, {-3, 7, 4}};  
    auto p = std::make_shared<Polygon3D>(il);  
    Screen s;  
    s.draw(p);  
    s.render();  
    //(2, 1, 6)  
    //(-3, 7, 4)}
```

Второй вариант
![[../Files/Pasted image 20220424114043.png]]
```cpp
using Screen = std::ostream;  
  
struct Vector3D {  
    int x, y, z;  
};  
  
void draw(int x, Screen &out, size_t position) {  
    out << std::string(position, ' ') << x << "\n";  
}  
  
void draw(Vector3D v, Screen &out, size_t position) {  
    out << std::string(position, ' ') << "(" << v.x << ", " << v.y << ", " << v.z  
        << ")\n";  
}  
  
class Drawable {  
    struct IDrawable {  
        virtual ~IDrawable() = default;  
        virtual std::unique_ptr<IDrawable> copy_() const = 0;  
        virtual void draw_(Screen &, size_t) const = 0;  
    };  
  
    struct DrawableInt final : IDrawable {  
        int data_;  
        DrawableInt(int x) : data_(std::move(x)) {}  
        std::unique_ptr<IDrawable> copy_() const override {  
            return std::make_unique<DrawableInt>(*this);  
        }  
        void draw_(Screen &out, size_t position) const override {  
            ::draw(data_, out, position);  
        }  
    };  
  
    struct DrawableVector3D final : IDrawable {  
        Vector3D data_;  
        DrawableVector3D(Vector3D x) : data_(std::move(x)) {}  
        std::unique_ptr<IDrawable> copy_() const override {  
            return std::make_unique<DrawableVector3D>(*this);  
        }  
        void draw_(Screen &out, size_t position) const override {  
            ::draw(data_, out, position);  
        }  
    };  
  
    std::unique_ptr<IDrawable> self_;  
  
public:  
    Drawable(int x) : self_(std::make_unique<DrawableInt>(std::move(x))) {}  
  
    Drawable(Vector3D x)  
            : self_(std::make_unique<DrawableVector3D>(std::move(x))) {}  
  
    // copy ctor, move ctor and assignment  
public:  
    Drawable(const Drawable &x) : self_(x.self_->copy_()) {}  
    Drawable(Drawable &&x) noexcept = default;  
    Drawable &operator=(Drawable x) noexcept {  
        self_ = std::move(x.self_);  
        return *this;  
    }  
  
public:  
    friend void draw(const Drawable &x, Screen &out, size_t position) {  
        x.self_->draw_(out, position);  
    }  
};  
  
using Model = std::vector<Drawable>;  
  
void draw(const Model &x, Screen &out, size_t position) {  
    out << std::string(position, ' ') << "<world>" << std::endl;  
    for (const auto &e : x)  
        draw(e, out, position + 2);  
    out << std::string(position, ' ') << "</world>" << std::endl;  
}  
  
int main() {  
    Model document;  
    document.push_back(0);  
    document.push_back(Vector3D{2, 1, 6});  
    document.push_back(Vector3D{-3, 7, 4});  
    document.push_back(3);  
    draw(document, std::cout, 0);  
    //<world>  
    //  0    
    //  (2, 1, 6)    
    //  (-3, 7, 4)    
    //  3    
    //</world>}
```

Третий вариант - обобщенный, с помощью шаблонов
![[../Files/Pasted image 20220424114125.png]]

```cpp
using Screen = std::ostream;  
  
struct Vector3D {  
    int x, y, z;  
};  
  
struct Polygon3D {  
    std::vector<Vector3D> vs;  
    Polygon3D(std::initializer_list<Vector3D> il) : vs(il) {}  
};  
  
void draw(int x, Screen &out, size_t position) {  
    out << std::string(position, ' ') << x << "\n";  
}  
  
void draw(Vector3D v, Screen &out, size_t position) {  
    out << std::string(position, ' ') << "(" << v.x << ", " << v.y << ", " << v.z  
        << ")\n";  
}  
  
void draw(const Polygon3D &p, Screen &out, size_t position) {  
    out << std::string(position, ' ') << "<polygon>" << std::endl;  
    for (auto v : p.vs)  
        ::draw(v, out, position + 2);  
    out << std::string(position, ' ') << "</polygon>" << std::endl;  
}  
  
class Drawable {  
    struct IDrawable {  
        virtual ~IDrawable() = default;  
        virtual std::unique_ptr<IDrawable> copy_() const = 0;  
        virtual void draw_(Screen &, size_t) const = 0;  
    };  
  
    template <typename T> struct DrawableObject final : IDrawable {  
        T data_;  
        DrawableObject(T x) : data_(std::move(x)) {}  
        std::unique_ptr<IDrawable> copy_() const override {  
            return std::make_unique<DrawableObject>(*this);  
        }  
        void draw_(Screen &out, size_t position) const override;  
    };  
  
    std::unique_ptr<IDrawable> self_;  
  
public:  
    template <typename T>  
    Drawable(T x) : self_(std::make_unique<DrawableObject<T>>(std::move(x))) {}  
  
    // copy ctor, move ctor and assignment  
public:  
    Drawable(const Drawable &x) : self_(x.self_->copy_()) {}  
    Drawable(Drawable &&x) noexcept = default;  
    Drawable &operator=(Drawable x) noexcept {  
        self_ = std::move(x.self_);  
        return *this;  
    }  
  
public:  
    friend void draw(const Drawable &x, Screen &out, size_t position) {  
        x.self_->draw_(out, position);  
    }  
};  
  
using Model = std::vector<Drawable>;  
  
void draw(const Model &x, Screen &out, size_t position) {  
    out << std::string(position, ' ') << "<world>" << std::endl;  
    for (const auto &e : x)  
        draw(e, out, position + 2);  
    out << std::string(position, ' ') << "</world>" << std::endl;  
}  
  
template <typename T>  
void Drawable::DrawableObject<T>::draw_(Screen &out, size_t position) const {  
    ::draw(data_, out, position);  
}  
  
int main() {  
    Model document;  
    document.push_back(0);  
    document.push_back(Polygon3D{{2, 1, 6}, {-3, 7, 4}});  
    document.push_back(document);  
    document.push_back(Vector3D{-3, 7, 4});  
    draw(document, std::cout, 0);  
    //<world>  
    //  0    
    //  <polygon>    
    //    (2, 1, 6)    
    //    (-3, 7, 4)    
    //  </polygon>    
    //  <world>    
    //    0    
    //    <polygon>    
    //      (2, 1, 6)    
    //      (-3, 7, 4)    
    //    </polygon>    
    //  </world>    
    //  (-3, 7, 4)    
    //</world>    
}
```
## **L**SP – Liskov substitution principle 
Частный класс должен иметь возможность свободно заменять общий, т.е. наследуясь от какого-то класса мы, также должны наследовать его логику.

```cpp
bool intersect(Polygon2D& l, Polygon2D& r); // 2D intersection  
class Polygon2D {  
    std::vector<double> xcoord, ycoord;  
// .... everything else ....  
};  
class Polygon3D : public Polygon2D {  
    std::vector<double> zcoord;  
// .... everything else ....  
}
```
В чем проблема? Неправильное [[Наследование C++]], в функцию intersect мы можем передать объекты Polygon3D, но она вычисляет пересечения 2D, а мы будем думать, что раз все хорошо, значит вычислилось пересечение 3D фигур.

[[Ковариантность и Контрвариантность]]

## **I**SP – interface separation principle
Тип не должен зависеть от тех интерфейсов, которые он не использует. Принцип плохо проектирования. Лучше наследоваться от нескольких интерфейсов, чем от одного общего.

```cpp
struct IWorker {  
    virtual void work() = 0;  
    virtual void eat() = 0;  
// .....  
};  
class Robot : public IWorker {  
    void work() override;  
    void eat() override {  
// do nothing  
    }  
};
class Manager {  
    IWorker *subdue;  
public:  
    void manage () {  
        subdue->work();
        subdue->eat();//Что будет, если у нас будет робот?
    }  
};
```

Робот наследуется от интерфейса работника, но не переопределяет все его [[Методы C++]], в этот момент нарушается ISP.

Принцип разделения интерфейса:
```cpp
struct IWorkable {  
    virtual void work() = 0;  
// .....  
};  
class Robot: public IWorkable {  
    void work() override;  
};
```

## **D**IP – dependency inversion principle 
Зависимость должна строиться на абстракциях, без использования реализации. Пример плохого проектирования:

![[../Files/Pasted image 20220424132528.png]]

Чем плохо? Sheduler зависит от Dag, изменится Dag, придется менять и метод shedule.

![[../Files/Pasted image 20220424132856.png]]

Зависимость должна быть только у абстрактных классов, то есть зависеть нужно от общего интерфейса, который не изменяется. Scheduler знает только об интерфейсе, следовательно то, что за этим интерфейсом легко заменить.