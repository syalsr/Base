# Как рендерит OpenGL

Сначала все было так:

```cpp
glClear(GL_COLOR_BUFFER_BIT);  
glBegin(GL_QUADS);  
glColor3f(1.0, 1.0, 1.0);  
for (auto Coord : Vertices)  
glVertex3fv(Coord);  
glEnd();

glfwSwapBuffers(Wnd.get());  
glfwPollEvents();
```

Но позже умные люди поняли, что не очень эффективно в Begin-End каждый раз отрисовывать кадр, даже если он полностью не меняется.

И придумали это:

![[../Files/Pasted image 20220424151029.png]]

1. Посылаем от API какую-то примитивную геометрию(треугольники, квадраты, линии)
2. Эти примитивы содержат вершины, вершины трансформируются во всякое
3. После чего вершины собираются и подаются на растеризатор
4. Растеризатор делает из них текстуры
5. И все попадает на Frame Buffer

Каждый блок схемы выше можно самому настраить

```cpp
glEnable(GL_DEPTH_TEST); 
glDepthFunc(GL_LESS); 
glEnable(GL_DEPTH_CLAMP); 
glEnable(GL_CULL_FACE); 
glClear(GL_COLOR_BUFFER_BIT);
```

