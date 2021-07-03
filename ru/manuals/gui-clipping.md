---
layout: manual
language: ru
github: https://github.com/defold/doc
title: GUI-клипирование
brief: В этом руководстве описано, как создавать GUI-ноды, которые маскируют другие ноды с помощью трафаретного клипирования.
---

# GUI-клипирование

GUI-ноды могут использоваться с целью *клипирования* других нод, то есть в качестве масок, влияющих на отображение других нод. В этом руководстве объясняется, как работает эта возможность.

## Создание ноды клипирования

Ноды Box, Text и Pie могут быть использованы для клипирования. Чтобы создать ноду клипирования, добавьте ноду в GUI, а затем задайте ее свойства соответствующим образом:

Clipping Mode
: Режим, используемый для клипирования.
  - `None` --- рендерит ноду без какого-либо клипирования.
  - `Stencil` --- заставляет ноду вписаться в текущую трафаретную маску.

Clipping Visible
: Отметьте, чтобы рендерить содержимое ноды.

Clipping Inverted
: Отметьте, чтобы вписать инверсию формы ноды в маску.

Затем добавьте ноду(ы), которую нужно клипировать, в качестве дочерней к ноде клипирования.

![Create clipping](/manuals/images/gui-clipping/create.png)

## Трафаретная маска

Клипирование работает за счет того, что ноды записывают данные в *трафаретный буфер*. Этот буфер содержит маски клипирования: информацию, которая сообщает видеокарте, должен ли пиксель быть отрендерен или нет.

- Нода без родителя-клиппера, но с режимом клипирования, установленным в `Stencil`, запишет свою форму (или ее инверсную форму) в новую маску клипирования, хранящуюся в трафаретном буфере.
- Если у клипирующей ноды есть клипирующий родитель, то вместо этого она будет клипировать клипирующую маску родителя. Клипирующая дочерняя нода никогда не будет _расширять_ текущую маску клипирования, только клипировать ее дальш.
- Ноды без клипирования, которые являются дочерними для клипируемых нод, будут рендериться с маской клипирования, созданной родительской иерархией.

![Clipping hierarchy](/manuals/images/gui-clipping/setup.png)

Здесь три ноды расположены в иерархической структуре:

- Шестигранная и квадратная формы --- это трафаретные ножницы.
- Шестиугольник создает новую маску клипирования, квадрат обрезает ее дальше.
- Нода окружности --- это обычная нода Pie, поэтому она будет отображаться с маской клипирования, созданной ее родительскими клипперами.

Для этой иерархии возможны четыре комбинации нормальных и инвертированных клиперов. Зеленая область отмечает часть окружности, которая рендерится. Остальная часть маскируется:

![Stencil masks](/manuals/images/gui-clipping/modes.png)

## Ограничения по использованию трафарета

- Общее количество трафаретных ножниц не может превышать 256.
- Максимальная глубина вложенности дочерних _трафаретных_ нод составляет 8 уровней. (Only nodes with stencil clipping count.)
- Максимальное число смежников ноды трафарета --- 127. Для каждого уровня иерархии трафаретов максимальное ограничение уменьшается вдвое.
- Инвертированные ноды имеют более высокую оценку. Существует ограничение на 8 инвертированных нод клипирования, и каждая из них уменьшает максимальное количество неинвертированных нод клипирования вдвое.
- Трафареты рендерят трафаретную маску из _геометрии_ ноды (не текстуры). Можно инвертировать маску, установив свойство *Inverted clipper*.


## Слои

Слои можно использовать для управления порядком рендеринга (и пакетирования) нод. При использовании слоев и нод клипирования обычный порядок наслоения отменяется.

- Порядок клипирования имеет приоритет над порядком слоев --- независимо от того, к какому слою принадлежит нода, она будет клипирована в соответствии с иерархией нод.
- Слои влияют только на порядок отрисовки графики --- и более того, слой, заданный в ноде клипирования, влияет только на порядок отрисовки _в иерархии этого клиппера_.

<div class='sidenote' markdown='1'>
Нода клипирования и ее иерархия будут отрисованы первыми, если ей назначен слой, и в обычном порядке, если слой не назначен.
</div>

![Layers and clipping](/manuals/images/gui-clipping/layers.png)

Здесь нода клиппера "ocular" установлена в "layer3", а нода "bean" --- в "layer1". Поэтому текстура клиппера "ocular" отрисовывается поверх обрезанного bean'.

Ноде "shield" задано "layer2", но это не влияет на порядок рендеринга ноды по отношению к "ocular" или "bean". Чтобы изменить порядок рендеринга ноды "shield", измените индексный порядок дерева нод.