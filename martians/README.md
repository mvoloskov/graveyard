# Тестовое задание Злых Марсиан

## Принятые решения  

### Стили
* Каждый компонент обернут в тег с классом `root`.

* Все цвета, тайминг-функции и подобные переиспользуемые вещи сохраняются в PostCSS-константы в начале файла стилей. Это позволяет легко менять значения, но в то же время не вредит переносимости компонентов, которая бы пострадала в случае создания одного глобального файла всех переменных.

* Тем не менее, шрифты и ресет вынесены в стили провайдера (`<App />`), потому что при перенесении компонентов в другой проект их дизайн определенно изменится, и шрифты будут другие. Компоненты должны работать и без специфического для нашего дизайна глобального ресета.

* Мобильные устройства обнаруживаются при помощи JavaScript, медиа-выражения не применяются. В то время как веб-сайты должны намеренно делаться _device-agnostic_, чему способствуют медиавыражения, веб-приложения должны строго разделять мобильные и стационарные устройства и предоставлять совершенно разный user experience в зависимости от этого.

### Приёмы и UX
* В приложение добавлен компонент Preview — отдельное окно предпросмотра поста, содержащее полный текст, статистику, информацию о времени публикации и о том, опубликован пост или нет. Оттуда же можно перейти к редактированию поста или удалить пост (при условии, что он еще не опубликован). Это сделано для того, чтобы избавить интерфейс от визуального мусора.

* При добавлении поста через универсальный слот (слот без времени), время ставится на десять минут позже текущего. Если через десять и менее минут наступит следующий день, пост не переносится в будущее — время устанавливается на значение 23:59, потому что следующий день будет иметь свой универсальный слот.

* При закрытии редактора контент не теряется. Для очистки редактора теперь есть отдельная кнопка.

* Кнопка публикации поста останется неактивной только при отсутствии текста. Если текст есть, мы дадим нажать на кнопку, но за этим последуют предупреждения о неправильной дате публикации, то есть попытке запланировать пост на прошлое, или не выбранных целевых соцсетях.

* Опубликованные посты помечаются галочкой, взятой из Evil Icons.

* Сетка с карточками способна содержать любое количество столбцов. Чтобы добиться этого эффекта, пришлось ставить внутренним элементам только правую и нижнюю границу, при этом левая граница всех блоков установлена на родителе, а верхняя граница — псевдоэлемент каждого блока. Это сделано именно так из-за того, что любой блок, дети которого переносятся, начинает занимать всю ширину.

* Слоты и карточки имеют разную границу: у слотов это пунктир, тогда как у карточек граница цельная. Из-за этого граница по-умолчанию, стоящая у родителя, сделана пунктиром, а цельная граница отрисовывается через сплошной `box-shadow`.

* Предполагается, что мы не можем редактировать или удалять опубликованные посты, хотя это не всегда так — некоторые современенные соцсети разрешают изменение уже опубликованного.

* Кнопка "закрыть" в превью — крестик, а не минус. Это сделано для консистентности — в редакторе постов крестик так же служит для закрытия, тогда как удаляет пост кнопка с корзиной.

* Нижний градиент, указывающий на то, что пост можно развернуть, появится только если текст поста достаточно длинный. Это достигается через градиентный фон фиксированного размера, который обрезается в случае с коротким текстом и влезает в случае с длинным.

* Увеличение карточки реализовано через `box-shadow`, потому что полноценная анимация увеличения ширины блока вызвала бы изменения в переносе текста, а это [почти всегда выглядит убого](https://codepen.io/uyouthe/pen/YBKVGE).

* Числительные в тексте слотов заменены цифрами, потому что количество слотов настраивается в конфигурации и может быть абсолютно любым, а вычислять числительное для каждого номера слота — [так себе затея](https://stackoverflow.com/questions/14766951/convert-digits-into-words-with-javascript/30524915#30524915).

* Логика проверки слотов так же заменена — теперь это не _строго совпадающие часы_, а окно длинною в час, по полчаса с каждой стороны. Старое решение было чисто "машинным", теперь оно имеет смысл и для человека. При старом алгоритме, слот в **14:00** отображался бы прямо за постом, запланированным на **13:59**.


### Конвенции
* Если стили меняются в зависимости от props, объявляется _десайдер_ — функция, возвращающая результирующую строку классов.

* Названия компонентов разнообразны и кратки. Например, компонент `Mole`, отвечающий за реализацию "всплывающей" анимации, называется так из-за ассоциации с игрой [Whack-A-Mole](https://en.wikipedia.org/wiki/Whac-A-Mole), цель которой — поразить "выпрыгивающие" фигурки.

* Корневой файл каждого компонента называется `index.js` для удобного импорта: `import Card from './Card'`.

* Компоненты и контейнеры хранятся отдельно друг от друга — при перенесении компонентов в другой проект нам совершенно незачем переносить его контейнер.

* Допускается вынесение повторяющихся частей разметки в отдельные субкомпоненты, при этом такой субкомпонент может находиться в этом же файле.

* Сложные элементы логики должны выноситься из шаблонов в отдельные чистые функции, определенные в отдельном файле. По умолчанию это `logic.js`.

* Логика никогда не экспортируется по умолчанию, потому что файл логики часто будет содержать некое множество небольших, независимых друг от друга функций-помощников. Экспортировать нужно только то, что используется в компоненте.

* Вместо того чтобы выбирать некий компонент из однотипного ряда при помощи громоздкого `switch`, разумнее [поместить весь ряд в объект](https://github.com/uyouthe/martians/blob/ec75ee1ae2a449c3dab2ddef460c3cc02146951a/src/components/Icon/component.js) и выбирать через обращение по ключу.

* Экшны вынесены в отдельные чистые функции и распределены по файлам в каталоге actions, по файлу на экшн. Они реквайрятся и пробрасываются в редьюсер.  


## Трейдоффы

* Не все сайд-эффекты вынесены. Все что связано со временем хранится в файле логики компонентов, писать для этого саги было бы слишком громоздко

* Аватар временно сделан статичным. Стоило бы хранить его в корне папки public и получать по адресу, потому что в продакшне он скорее всего будет доставляться именно так, как и любая другая картинка  

* В системе один аватар на все соцсети. Это не всегда так в реальном мире, но копировать одну и ту же картинку пять раз я считаю нецелесообразным  

* Мобильные стили, как и любые другие стили для разных состояний, таких как preview, применяются в этом же файле стилей. Я пока не знаю, как компоновать стили правильно. Из-за этого файлы стилей получились громоздкими


## Претензии к макету
* Слишком много оттенков серого, не прослеживается система

* Нет свотчей, нет заданной отдельно цветовой схемы

* Нет шпаргалки компонентов в разных состояниях. Состояния элементов управления не прорисованы вообще

* Нет руководства по системе отступов

* Градиент Инстаграма неофициальный, тогда как все остальные цвета официальные

* Где-то используются Evil Icons, где-то — другие иконки. Правильнее было бы использовать только Evil Icons, это позволило бы подключить их как зависимость

## Сторонние ресурсы

[Иконка стирательной резинки](https://thenounproject.com/term/eraser/185896/) сделана компанией [Icon54](https://icon54.com/) и распространяется по лицензии CC-BY.
