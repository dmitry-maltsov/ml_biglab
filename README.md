# Постановка задачи Intelligent Placer
Требуется создать “Intelligent Placer”: по поданной на вход фотографии нескольких предметов на светлой горизонтальной поверхности и многоугольнику понимать, можно ли расположить одновременно все эти предметы на плоскости так, чтобы они влезли в этот многоугольник. Предметы и горизонтальная поверхность, которые могут оказаться на фотографии, заранее известны. Также заранее известно направление вертикальной оси Z у этих предметов.

Многоугольник задается массивом с координатами вершин на естественной плоскости.

Intelligent Placer должен быть оформлен в виде python-библиотеки intelligent_placer_lib, которая поставляется каталогом intelligent_placer_lib с файлом intelligent_placer.py, содержащим функцию - точку входа def check_image(<path_to_png_jpg_image_on_local_computer>[, <poligon_coordinates>])
которая возвращает True если предметы могут влезть в многоугольник, иначе False.

Таким образом, на вход программа должна получать путь к изображению и массив с координатами вершин многоугольника. На выход программа возвращает False, еcли объекты на изображении не помещаются в указаноном многоугольнике и True, если помещаются.

## Требования к изображениям:

1) Качество изображений - минимум Full HD
2) Объекты должны быть польностью видны на изображениях
3) Фон изображений - монотонный белый
4) Края объектов должны быть четко различимы
5) Объекты не должны перекрываться или касаться друг друга
6) Объекты могут вращаться **только** вокруг оси, перпендикулярной плоскости, на которой они расположены
