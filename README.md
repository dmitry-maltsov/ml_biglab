# Постановка задачи Intelligent Placer

## Общая формулировка:

Требуется создать “Intelligent Placer”: по поданной на вход фотографии нескольких предметов на светлой горизонтальной поверхности и многоугольнику понимать, можно ли расположить одновременно все эти предметы на плоскости так, чтобы они влезли в этот многоугольник. Предметы и горизонтальная поверхность, которые могут оказаться на фотографии, заранее известны. Также заранее известно направление вертикальной оси Z у этих предметов.

Intelligent Placer должен быть оформлен в виде python-библиотеки intelligent_placer_lib, которая поставляется каталогом intelligent_placer_lib с файлом intelligent_placer.py, содержащим функцию - точку входа def check_image(<path_to_png_jpg_image_on_local_computer>[, <poligon_coordinates>])
которая возвращает True если предметы могут влезть в многоугольник, иначе False.

Таким образом, на вход программа должна получать путь к изображению и массив с координатами вершин многоугольника. На выход программа возвращает False, еcли объекты на изображении не помещаются в указаноном многоугольнике и True, если помещаются.

## Требования к изображениям:

1. Объекты должны быть польностью видны на изображениях;
2. Фон изображений - монотонный белый лист размера A4;
3. Края объектов должны быть четко различимы;
4. Объекты не должны перекрываться или касаться друг друга;
5. Объекты могут вращаться **только** вокруг оси, перпендикулярной плоскости, на которой они расположены;
6. Одни и те же объекты могут повторяться сколько угодно раз;
7. На фотографии может не быть ни одного объекта, тогда алгоритм должен считать что нужно вписать **0** объектов в многоугольник и вернуть True;
8. На фотографии не должно быть посторонних объектов, все объекты, которые могут быть на фотографии, находятся в папке data/objects;
9. Объектив фотоаппарата направлен перпендикулярно поверхности, на которой расположены объекты;
10. Съемка примитивов объектов(data/objects) и фона(data/background.jpg) должна быть сделана на равном расстоянии от объектива фотоаппарата;

## Требования к многоугольнику:

1. Многоугольник задается массивом с координатами вершин на естественной плоскости;
2. Минимальное колличество вершин - 3, максимальное - 7;
3. Любая вершина не должны быть коллинеарна с двумя соседними вершинами;
4. Координаты вершин задаются по "часовой стрелке";
5. Многоугольник должен быть простым и выпуклым;

# План решения поставленной задачи:

## Обработка изображений:

1. Вычисление размера фона:
  1. Программа принимает изображение фона(data/background.jpg);
  2. Понижение разрешения изображения (в 5-10 раз) для упрощения поиска границ объектов;
  3. Бинаризация изображения;
  4. Анализ компонент связности на маске;
  5. Удаление шумов с помощью морфологических операций;
  6. Нахождение границ фона с помощью преобразований Хафа;
  7. Вычисление расстояния между горизонтальными линиями и расстояния между вертикальными линиями, соответствующими границам фона. Таким образом будут получены размеры фона в условных единицах;
2. Работа с примитивами:
  1. Программа принимает примитивы изображений из папки data/objects;
  2. Понижение разрешения изображений (в 5-10 раз) для упрощения поиска границ объектов;
  3. Бинаризация изображений;
  4. Поиск и сохранение масок примитивов объектов с применением Гаусса, морфологических операций и анализа компонент связности;
  5. Разбивка найденных масок объектов на треугольники с помощью триангуляции Делоне. Вычисление площади масок, площади будут равны сумме площадей полученных при триангуляции треугольников. Таким образом будут получены площади объектов в условных единицах. Маски примитивов теперь предствляют из себя многоугольники;
  6. Зная площадь фона в условных единицах, площадь объекта в условных единицах, а также площадь фона в см^2 (лист А4 - 623.7 см^2), а также при условии, что примитивы и фон были сфотографированы на одинаковом расстоянии от объектива, вычисляются площади примитивов в см^2;
5. Работа с данными в датасете:
  1. Программа принимает изображения из папки data/dataset;
  2. Понижение разрешения изображений (в 5-10 раз) для упрощения поиска границ объектов;
  3. Бинаризация изображений;
  4. Нахождение объектов на изображении и составление их масок с помощью Гаусса, морфологических операций и анализа компонент связности;
  5. Разбивка найденных масок объектов на треугольники с помощью триангуляции Делоне;
  6. Сравнение масок, полученных в предыдущем пункте с масками, полученными в пункте 2.4. и соотношение объектов, найденных на изображении с масками из пункта 2.4. При этом маски, полученные в предыдущем пункте, могут масштабироваться. Таким образом будут использоваться маски примитивов, для которых известны площади в см^2;

## Алгоритм Intelligent Placer:
1. Программа принимаем массив с координатами многоугольника, в который нужно вписать объекты на изображении из датасета;
2. Вычисляется сумма площадей масок объектов, найденных на изображении из датасета, вычисляется площадь заданного координатами многоугольника по формуле Гаусса. Если площадь многоугольника меньше, чем суммарная площадь объектов, программа возвращает False;
3. Генетический алгоритм для решения задачи оптимизации:
  1. Генерация N моделей: расположение многоугольников примитивов случайным образом, так, чтобы они не пересекались;
  2. Вычисление фитнесс функций - числа вершин, лежащих вне "большого" многоугольника;
  3. Отбор лучших моделей. Параметры мутации - угол поворота примитива и координата первой вершины многоугольника примитива;
  4. Повторение пунктов 2 и 3, пока фитнесс функция не станеть равна 0 или не пройдёт M итераций;
4. Если фитнесс функция лучшей модели после оптимизации равна 0, программа возвращает True, иначе - False;

